<div align="center">
	<p>
		<img alt="Thoughtworks Logo" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/thoughtworks_flamingo_wave.png?sanitize=true" width=200 />
    <br />
		<img alt="DPS Title" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/EMPCPlatformStarterKitsImage.png" width=350/>
	</p>
  <h3>authoring orbs</h3>
  <h5>3. continuous integration and publishing</h5>
</div>
<br />

### Orb

1. Copy the [pipeline starting template](/doc/config.yml) and edit for your orb and context name.

Git push will trigger the first four, concurrent jobs (static analysis of the orb), followed by publishing an @dev:SHA tagged version of the orb. If all those steps are successful, the continuation step will trigger the integration-test pipeline.  

```yaml
      - orb-tools/lint:
          filters: *on-push-main

      - orb-tools/review:
          max_command_length: 100
          exclude: RC010
          filters: *on-push-main

      - shellcheck/check:
          filters: *on-push-main

      - orb-tools/pack:
          filters: *on-push-main

      - orb-tools/publish:
          name: publish development package
          context: *context
          orb_name: *orb-name
          vcs_type: << pipeline.project.type >>
          filters: *on-push-main
          requires:
            - orb-tools/lint
            - orb-tools/review
            - shellcheck/check
            - orb-tools/pack

      - orb-tools/continue:
          name: Launch integration test pipeline
          context: *context
          orb_name: *orb-name
          config_path: .circleci/integration-test.yml
          pipeline_number: << pipeline.number >>
          vcs_type: << pipeline.project.type >>
          filters: *on-push-main
          requires:
            - publish development package
```

2. Copy the [integration-test pipeline template](/doc/integration-test.yml) and create tests for each command, job, and executor where appropriate.

Example: steps to call command that installs tool and then confirm healthy result  
```yaml
- op/install:
    op-version: 2.26.1
- run:
    name: test version install
    command: |
    set -exo pipefail
    op --version | grep "2.26.1"
```

The latest dev version of an orb can also be pulled on @dev:alpha - keep in mind, dev versions are automatically deleted from the circleci orb registry after 90 days.

3. Create a release version by tagging the repository with vX.X.X semantic version.

3. Consider the following bash script for quick, local syntax checks.

```bash
#!/usr/bin/env bash
circleci orb pack src
circleci orb pack src > orb.yml
circleci orb validate orb.yml
rm orb.yml
```

### Action

1. static analysis.  

If you copied the gha starter kit then you have a `git push` triggered pipeline all ready setup to perform static analysis. The gha-tools-action static analysis workflow will perform the following checks:  

* yamllint  
* shellcheck  
* ossf/scorecard (and upload results to the security dashboard of the repo)  

Notice that the workflow includes before and after parameters for passing custom scripts if needed.  

```yaml
- name: run custom before-analysis scripts
  working-directory: ${{ inputs.working-directory }}
  shell: bash
  run: ${{ inputs.before-static-analysis }}

- name: gha-tools-action/lint
  uses: ThoughtWorks-DPS/gha-tools-action/lint@421a368581ea29881a5d70ef1744d90d874c5703     # v0.1.0

- name: gha-tools-action/check
  uses: ThoughtWorks-DPS/gha-tools-action/check@421a368581ea29881a5d70ef1744d90d874c5703     # v0.1.0
  with:
    working-directory: ${{ inputs.working-directory }}
    shellcheck-version: ${{ inputs.shellcheck-version }}
    shellcheck-exclude: ${{ inputs.shellcheck-exclude }}
    shellcheck-ignore-paths: ${{ inputs.shellcheck-ignore-paths }}
    shellcheck-ignore-names: ${{ inputs.shellcheck-ignore-names }}
    shellcheck-severity: ${{ inputs.shellcheck-severity }}
    shellcheck-check-together: ${{ inputs.shellcheck-check-together }}
    shellcheck-scandir: ${{ inputs.shellcheck-scandir }}
    shellcheck-additional-files: ${{ inputs.shellcheck-additional-files }}
    shellcheck-format: ${{ inputs.shellcheck-format }}

- name: gha-tools-action/scorecard
  uses: ThoughtWorks-DPS/gha-tools-action/scorecard@421a368581ea29881a5d70ef1744d90d874c5703     # v0.1.0

- name: run custom after-analysis scripts
  working-directory: ${{ inputs.working-directory }}
  shell: bash
  run: ${{ inputs.after-static-analysis }}
```

If these three checks pass then the integration-test pipeline will automatically be called.  

2. integration-tests

Edit the copied pipeline to add tests as appropriate for each action and workflow you add to your action repository. Many actions can be very difficult to test. Depending on the type of Action limited or even using the failure of the action itself as the sole check can be appropriate.  

Example: 
```yaml
# yamllint disable rule:line-length
# yamllint disable rule:truthy
---
name: integration tests

on:
  workflow_call:

    secrets:
      OP_SERVICE_ACCOUNT_TOKEN:
        description: 1password vault services account token
        required: false

permissions:
  contents: read

jobs:

  static-code-analysis:
    name: action integration tests
    runs-on: ubuntu-latest

    env:
      OP_SERVICE_ACCOUNT_TOKEN: ${{ secrets.OP_SERVICE_ACCOUNT_TOKEN }}

    steps:
      - name: checkout code
        uses: actions/checkout@0ad4b8fadaa221de15dcec353f45205ec38ea70b         # v4.1.4

      - name: Install 1Password CLI
        uses: 1password/install-cli-action@143a85f84a90555d121cde2ff5872e393a47ab9f      # v1.0.0
        with:
          version: 2.27.0

      - name: use action             
        uses: # call your action here
        run: echo "tests"

      - name: test result
        shell: bash
        run: # insert a script that confirms the expected results of the action
```
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
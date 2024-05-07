<div align="center">
	<p>
		<img alt="Thoughtworks Logo" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/thoughtworks_flamingo_wave.png?sanitize=true" width=200 />
    <br />
		<img alt="DPS Title" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/EMPCPlatformStarterKitsImage.png" width=350/>
	</p>
  <h3>authoring orbs</h3>
  <h5>2. project setup</h5>
</div>
<br />


### Orb setup  

1. Create a new repository
2. Create new orb entry in circleci orb registry `circleci orb create <namespace>/<orb> [--private]`. See [documentation](https://circleci.com/docs/create-an-orb/).
3. Use `circleci orb init` to generate the starting folder/file structure
4. Edit ISSUE and PR templates for correct org, repo and orb naming
5. Use .yml as the file extension rather than .yaml, following the orb-tools convention
6. Add pre-commit style web hooks for local code analysis. [Example](/doc/pre-commit-config.yaml).

### Action setup

1. Create a new repository
2. Clone the contents of [psk-kit-github-action](https://github.com/ThoughtWorks-DPS/psk-kit-github-action)
4. Edit ISSUE and PR templates for correct org, repo and action naming
4. Add pre-commit style web hooks for local code analysis. [Example](/doc/pre-commit-config.yaml).

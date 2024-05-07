<div align="center">
	<p>
		<img alt="Thoughtworks Logo" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/thoughtworks_flamingo_wave.png?sanitize=true" width=200 />
    <br />
		<img alt="DPS Title" src="https://raw.githubusercontent.com/ThoughtWorks-DPS/static/master/EMPCPlatformStarterKitsImage.png" width=350/>
	</p>
  <h3>authoring orbs and actions</h3>
  <h5>1. design goals</h5>
</div>
<br />

Think about what kind of resource you want to create.   

| Specific Tool or Process | Collection | Build or Domain Workflows |
|--------------------------|------------|---------------------------|
| One or more commands that implement specific patterns or best-practice uses of a specific tool or process. For tools, this should include installation. | A collection of commands, executors, or jobs that are useful across many types of pipelines and create a better user experience when bundled together. | Complete language or resource specific workflows, including non or cross-functional requirements |
| Orbs: [orb-1password](https://github.com/ThoughtWorks-DPS/orb-1password) | Orbs: [orb-image-provenance](https://github.com/ThoughtWorks-DPS/orb-image-provenance), [orb-pipeline-events](https://github.com/ThoughtWorks-DPS/orb-pipeline-events), [orb-base-packages](https://github.com/ThoughtWorks-DPS/orb-base-packages) | Orbs: [orb-tools](), [orb-terraform](https://github.com/ThoughtWorks-DPS/orb-terraform), [orb-python-api](https://github.com/ThoughtWorks-DPS/orb-python-api) |
| Action: [1passwork-actions](https://github.com/ThoughtWorks-DPS/1password-action) | Action: [common-actions](https://github.com/ThoughtWorks-DPS/common-actions) | Action: [gha-tools-action](https://github.com/ThoughtWorks-DPS/gha-tools-actions) |

Keep in mind the "dumb pipes, smart scripts" architectural goals. Generally, steps that would contain bash scripts more than a line or two long should be incorporated via the src/scripts folder. The orb-tools/review job does a pretty good job checking for this but can overly sensitive.  

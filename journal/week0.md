# Week 0 — Billing and Architecture

Gipod has been changed to ONA. Solved environment and initialization issue for AWS CLI by changing configuration from .gitpod.yml to devcontainer.json. 
Added in devcontainer.json the following:
"postCreateCommand": "cd /workspaces && curl \"https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip\" -o \"awscliv2.zip\" && unzip awscliv2.zip && sudo ./aws/install && cd /workspaces/AWS-CloudCamp",   
"containerEnv": {
		"AWS_CLI_AUTO_PROMPT": "on-partial"
	},
  
Environment variables can't be created using gp command, so instead added in project->edit->secrets->new secret and added as environment variables.

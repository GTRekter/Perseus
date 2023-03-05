---
layout: page
---

# Terraform Plan commented in PR
Have you ever wanted to double-check the Terraform plan before approving a PR without digging into the pipeline's logs? This code snippet uses the AzDO APIs to post the plan as a comment on the PR, making checking it easier. 
```yaml
- script: |
    terraform plan -out tf.tfplan
  displayName: Generate Terraform plan

- script: |
    terraform show -no-color tf.tfplan > $(Agent.TempDirectory)/tf.txt
  displayName: Convert Terraform plan to text

- bash: |
    cd $(Agent.TempDirectory)
    ENCODED_URL=$(echo "$(System.CollectionUri)$(System.TeamProject)/_apis/git/repositories/${{ variables.SourceRepositoryName }}/pullRequests/$(System.PullRequest.PullRequestId)/threads?api-version=7.0" | sed 's/ /%20/g')
    jq --rawfile comment tf.txt '.comments[0].content=$comment' <<< '{"comments": [{"parentCommentId": 0,"content": "","commentType": 1}],"status": 1}' |
    curl --request POST "$ENCODED_URL" \
    --header "Content-Type: application/json" \
    --header "Accept: application/json" \
    --header "Authorization: Bearer $SYSTEM_ACCESSTOKEN" \
    --data @- \
    --verbose
  env:
    SYSTEM_ACCESSTOKEN: $(System.AccessToken)
  displayName: 'Post comment with Terraform Plan'
```

# Check connectivity and save agent IP's
This snippet will provision the infrastructure define in a module (and relative dependencies) and check the connectivity of it's virtual machine.
```yaml
- task: TerraformCLI@0
  displayName: Apply Terraform plan - Virtual Machine
  inputs:
      command: 'apply'
      workingDirectory: '/'
      environmentServiceName: 'sp-training-kr-01'
      commandOptions: '-target=module.core_module.module.linux_virtual_machines'
      allowTelemetryCollection: false

- task: TerraformCLI@0
  displayName: Make Terraform outputs available in the pipeline
  inputs:
      workingDirectory: '/'
      command: output

- bash: |
    echo 'Pinging $(TF_OUT_VIRTUAL_MACHINE_PRIVATE_IP_ADDRESS)'
    ping -c 1 $(TF_OUT_VIRTUAL_MACHINE_PRIVATE_IP_ADDRESS) 2>/dev/null 1>/dev/null
    if [ "$?" = 0 ]
    then
    echo "The Virtual machine scale set was able to connect to the server. Check the configuration"
    exit 1
    else
    echo "The Virtual machine scale set wasn't able to connect to the server."
    fi
displayName: 'Ping the virtual machine'  

- bash: |
    IP=$(hostname -I | cut -d ' ' -f 1)
    echo "##vso[task.setvariable variable=azure_vmss_isntance_ip;]$IP"
    echo "This agent public IP is: $(azure_vmss_isntance_ip)"
displayName: 'Save agent IP'
```


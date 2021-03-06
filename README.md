# Akamai Property Manager CLI

* [Overview](#overview)

* [Available commands](#available-commands) 

* [Stay up to date](#stay-up-to-date)

* [Workflows](#workflows)

    * [Federated development workflow](#federated-development-workflow)

    * [Akamai Pipeline workflow](#akamai-pipeline-workflow)    

* [Get started](#get-started)

* [Installing Akamai Property Manager CLI](#installing-akamai-property-manager-cli)

* [Set up federated development](#set-up-federated-development)

    * [Create snippets of your Property Manager configuration](#create-snippets-of-your-property-manager-configuration)

    * [Add a new snippet](#add-a-new-snippet)

* [Create and set up a new pipeline](#create-and-set-up-a-new-pipeline)

    * [Update the variableDefinitions.json file](#update-the-variabledefinitions-file)
	
	* [Common product IDs](#common-product-ids)

    * [Save and promote changes through the pipeline](#save-and-promote-changes-through-the-pipeline)

* [How you can use this CLI](#how-you-can-use-this-cli)

    * [Using Property Manager user variables with Akamai Pipeline
](#using-property-manager-user-variables-with-akamai-pipeline)

    * [Using attributes that vary across environments with Akamai Pipeline](#using-attributes-that-vary-across-environments-with-akamai-pipeline)

    * [Reuse secure edge hostnames](#reuse-secure-edge-hostnames)

    * [Working with advanced behaviors](#working-with-advanced-behaviors)	
	
* [Notice](#notice)

# Overview 

The Property Manager CLI lets you make configuration changes locally and automate the deployment of Akamai property changes across your local environments.  

With this client-side application, you can: 

* **Set up federated development.** You can create local client side templates, or snippets, for different parts of your property configuration, like rules or behaviors. Different teams within your organization can own specific snippets and make Property Manager changes both independently of and in parallel with other teams.

* **Validate locally.** Before deploying a change, the Property Manager CLI automatically validates your configuration syntax locally.

* **Automate a pipeline.** A pipeline is a linked and ordered chain of environments.  A typical pipeline starts with local development environments, moves to QA and Integration environments, then finishes with your production environment. The number of environments you deploy to depends on your organization’s specific needs.

* **Customize variable definitions between environments.** If you want a use one variable value in a development environment and try out a different value in a test environment, you can add a custom variable to your Property Manager CLI template files. 

You use `akamai pipeline` command to make changes within an automated Akamai Pipeline. Soon you’ll be able to edit Property Manager configurations locally for an existing property with the `akamai property-manager` command. See the [Workflows](#workflows) section for more information.

# Available commands 
Want to see all available CLI commands? See the [help for this CLI](docs/cli_command_help.md).

# Stay up to date
To make sure you always use the latest version of the CLI, run this command: 
`akamai update property-manager`

# Workflows

With this CLI, you’ll likely use one of these common workflows: 

* **Federated development.** Use this workflow to make local configuration changes for one property, enable different teams to own different parts of the configuration, and deploy without interdependencies.

* **Akamai Pipeline.** Use this workflow to both set up configuration templates and create an automated pipeline for deploying Property Manager changes to your various environments. 

## Federated development workflow

Here’s a typical workflow when you want to break your Property Manager configuration into separate rules-based snippets:

1. Make some decisions:

    * Which property configuration do you want to create snippets for? 
	
    * How do you want to divide up the configuration? By default, a separate snippet is created for each top level rule in your configuration. You can add, remove, or modify snippets. 
	
    * Do you need any new supporting processes?  

1. Run the `akamai property-manager new-property` command to create a local instance of your configuration. 


1. Verify that the `/config-snippets` folder contains a separate JSON-based configuration snippet for each rule in your property configuration. <br> Within this folder, the main.json file ties all the snippets together: It lists the available snippets and contains the local permissions for each snippet. You can modify both the list of snippets and their permissions.

1. Edit the snippets as needed to reflect the rule changes you want to deploy. <br> If another team owns a snippet, once they make their changes locally, they can copy it into the `/config-snippets` folder. 
 
1. Run the `akamai property-manager activate` command to activate the property. This command syncs the local changes in your `/config-snippets` directory to the Akamai network. Once activation is complete, you can verify changes in the Property Manager UI. 

## Akamai Pipeline workflow

Here’s a typical workflow for using Akamai Pipeline once you install and configure the CLI:

1. Make some decisions: 

    * Do you want to use an existing property or a specific product as a template for your pipeline?

    * Which environments to you want to include? 

    * What do you want to call your pipeline? 

2. Create a new pipeline based on your decisions. If you’re using an existing property as a template, you’ll need the property ID or name. If you’re using a product as a template, you’ll need account information, like contract ID. The CLI includes commands for retrieving account-specific IDs. <br>

	**Note:** The new pipeline adds one new Akamai property for each environment. The naming convention of the property is `<environment_name>.<pipeline_name>`.   

3. In the pipeline’s `environments` folder, edit the `variableDefinitions.json` file to define and reflect the attributes shared across the pipeline. 

4. In the folders created for each environment, edit the `variables.json` file to reflect the settings specific to that environment.

5. In the `templates` folder, make your desired code change in the JSON-based configuration snippets.

6. Promote the change to the first environment. Promoting saves and activates your change, and propagates it to the next environment in your pipeline.

7. Verify that the change was successfully promoted to the first environment, and test the change.

8. Complete steps 6 and 7 for each additional environment in the pipeline.

# Get started 

In order to start using Akamai Pipeline, you have to complete these tasks: 

* Verify you have a Unix-like shell environment, like those available with Mac OS X, Linux, and similar operating systems.

* Set up your credential files as described in [Get Started with APIs](https://learn.akamai.com/en-us/learn_akamai/getting_started_with_akamai_developers/developer_tools/getstartedapis.html), and include authorization for the Property Manager API. 

* Install the [Akamai CLI tool](https://github.com/akamai/cli).

* Install [Node Version Manager](http://nvm.sh/) (NVM), which lets you run applications with different node version requirements side by side.

* Install [Node.js](https://Nodejs.org/en/) version 8.0 Long Term Support (LTS). 

# Installing Akamai Property Manager CLI

You use the Akamai CLI tool to install the code. Once you complete the installation, you can use the `akamai pipeline` commands.

Here’s how to install Akamai Pipeline:

1. Create a project folder under your user home directory: `mkdir <folder_name>`. For example: 
`mkdir akamai_pipeline`. <br> You’ll run Property Manager CLI commands from this folder, which also contains the default values for the CLI and a separate subdirectory for each pipeline you create. 

2. Run the installation command: `akamai install property-manager`

3. Verify that the CLI is set up for your OPEN API permissions:

    1. Run this command to return the list of contracts you have access to: 
            akamai property-manager list-contracts
        
    1. Verify that the list of contracts returned is accurate for your access level. 

4. Continue by either [creating snippets for federated deployment](#set-up-federated-development) or [setting up a new pipeline](#create-and-set-up-a-new-pipeline).
	
	
# Set up federated development

Create your local client side snippets to let different teams own different parts of your property configuration. 

## Create snippets of your Property Manager configuration

1. Run the `akamai property-manager new-property` command to create a local instance of your Property Manager configuration. 

1. In your project directory structure, navigate to the new `config-snippets` folder. <br> This folder contains a separate JSON-based configuration snippet for each rule in your property configuration.

1. Verify the folder structure, which will look something like this:

        project_name/
            /config-snippets
                main.json
                compression.json
                static.json
				cloudlets.json
                ...
		
    Each snippet represents one of the property's child rules.		
				
1. If needed, add or edit snippets to support your organization's federated development structure. <br> Say, for example, you have a `cloudlets.json` snippet that only includes Edge Redirector. Since Marketing maintains this Cloudlet, you'll change the filename to  `mkt-EdgeRedirect.json` to note the owner and the application the rule is for.  

1. Open the `main.json` file, which corresponds to the property's default rule, edit the rule values as needed, and update the `children` array to reflect any snippet name changes, [additions](#add-a-new-snippet), or deletions.  

1. If required, set up local permissions for each JSON snippet.

1. Run the `akamai property-manager activate` command to activate your changes. <br> It's a good practice to test your changes before activating on production. 

1. If you'd like, verify your changes in the Property Manager UI. 

## Add a new snippet

If you want to add a new snippet to your federated development configuration: 

1. Create a JSON file for the new snippet that represents a [Property Manager rule](https://developer.akamai.com/api/core_features/property_manager/v1.html#rule), including any child rules. <br> For example: 

	```
	{
		"name": "Catalog",
		"children": [],
		"behaviors": [
			{
				"name": "timeout",
				"options": {
					"value": "5s"
				} } ],
		"criteria": [
			{
				"name": "path",
				"options": {
					"matchOperator": "MATCHES_ONE_OF",
					"values": [
						"/catalog/*"
					],
					"matchCaseSensitive": false
				} } ],
		"criteriaMustSatisfy": "all"
	}
	```

1. Open the `main.json` file.

1. Add the snippet to the `children` object:

    ```
	"rules": {
			"name": "default",
			"children": [
				"#include:Cache_extensions.json",
				"#include:Catalog.json",
				"#include:Furniture.json",
			    "#include:Clothing.json",
				],
			"Behaviors": [ 
			…
			… ],
			}
    ```
	
1. Save your changes.

1. Run the `akamai property-manager activate` command to activate your changes.


# Create and set up a new pipeline

You use the CLI to create a new pipeline. Before creating any type of pipeline, you’ll need the names of two or more environments that you want to include. If you're using a product as a template, you'll also need group, product, and contract IDs. If you're using a property as a template, you'll need either the property name or ID.

**Note:** You only have to create a pipeline once.

## Create a new pipeline

To create a new pipeline:  

1. If you need to, retrieve and store the contract, group, and product IDs for your pipeline:

    1. Run this command to list contract IDs (contractId):  `akamai pipeline list-contracts`

    2. Run this command to list group IDs (groupId):  `akamai pipeline list-groups`

    3. Run this command to [list product IDs](#common-product-ids) (productId): 
            akamai pipeline list-products -c <contractId>

	**Note:** The IDs returned depend on the permissions associated with your username.

2. Determine which environments you want to include in your pipeline, and what you want to name them. The environment names you choose are used in the pipeline’s directory structure.

3. Choose a descriptive name for your pipeline. <br> The pipeline name is added as a suffix to each property created for your new pipeline. Because of this, make sure the name you choose won’t result in any duplicate property names for the account. So, if your pipeline is `example.com`, and your environments are `dev`, `qa`, and `www`, the new properties will be `dev.example.com`, `qa.example.com`, and `www.example.com`.

4. If creating a pipeline using a specific product as a template, run this command:  
        akamai pipeline new-pipeline -c <contractId> -g <groupId> -d <productId> -p <pipelineName> <environmentName1 environmentName2...>

    For example, if you want to base your pipeline on Ion, you'd enter a command like this: 
        akamai pipeline new-pipeline -c 1-23ABC -g 12345 -d SPM -p MyPipeline123 qa prod

1. If creating a pipeline using an existing property as a template, run this command:  
        akamai pipeline new-pipeline -p <pipelineName> -e <propertyId or propertyName> <environment1_name environment2_name...>
    
    For example: `akamai pipeline new-pipeline -p MyPipeline123 -e 123 qa prod`

6. Verify the pipeline folder structure, which will look something like this:

    ```
        pipeline_name/
            projectInfo.json
            /cache
            /dist
            /environments
                variableDefinitions.json
                /environment1_name
                    envInfo.json
                    hostnames.json
                    variables.json
                    ...
               /environment2_name
                    envInfo.json
                    hostnames.json
                    variables.json
                    ...
                   
            /templates
                main.json
                compression.json
                ...
                static.json
    ```

7. Edit `variableDefinitions.json` to declare variables that you can use in any of the templates. <br> See 
[Update the variableDefinitions.json file](#update-the-variabledefinitions-file) for details.

8. In each environment-specific subdirectory under the `/environments` folder, edit these JSON files:                      

<table>
  <tr>
    <th>File</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>hostnames.json</code></td>
    <td>Contains a list of hostname objects for this environment. 
<br> If the <code>edgeHostnameId</code> is <code>null</code>, the CLI creates edge hostnames for you. If you want to use an existing edge hostname, set both the <code>cnameTo</code> and <code>edgeHostnameId</code> values accordingly. </td>
  </tr>
  <tr>
    <td><code>variables.json</code></td>
    <td>After editing your <code>variableDefinitions.json</code> file, update this file with the actual values for the environment, like content provider (CP) code, origin hostnames, and any additional variables you added.  
<br> <b>Note:</b> The CLI throws errors if there is a discrepancy between this file and the environment’s <code>variableDefinitions.json</code> file. For example, an error would occur if there’s a variable in the <code>variables.json</code> file that isn’t declared in the <code>variableDefinitions.json</code> file.
 </td>
  </tr>
</table>

## Update the variableDefinitions file 
Update `variableDefinitions.json` to declare variables that you can use in any of the templates.

As a general rule:

<table>
  <tr>
    <th>If an attribute has...</th>
    <th>Then...</th>
  </tr>
  <tr>
    <td>the same value across all environments and is already in the <code>variableDefinitions.json</code> file.</td>
    <td>Provide the default value in <code>variableDefinitions.json</code>. <br> 
	<b>Note:</b> You can still override the default value in an environment’s <code>variables.json</code> file.</td>
  </tr>
  <tr>
  <td>different values across environments and is already in the <code>variableDefinitions.json</code> file.</td>
    <td>Set the default to <code>null</code> in <code>variableDefinitions.json</code> and add the environment-specific value to each <code>variables.json</code> file.</td>
  </tr>
  <tr>
  <td>different values across environments and does not exist in the <code>variableDefinitions.json</code> file.</td>
  <td><ol><li>Parameterize it inside your template snippets using <code>“${env.&lt;variableName&gt;}"</code><br>For example: <code>"ttl": “${env.ttl}"</code></li>
  <li>Add it to <code>variableDefinitions.json</code> and set it to <code>null</code>. You can set the type to anything you choose.</li><li>Add it to your environment-specific <code>variables.json</code> files and set the individual values.
</li></ol></td>
  </tr>
  </table>

## Common product IDs 
If creating a new pipeline based on a product template, you’ll need to know your product ID. Here are some of the more commonly-used ones:

<table>
  <tr>
    <th>Product</th>
    <th>Code</th>
  </tr>
  <tr>
    <td>Ion</td>
    <td>SPM</td>
  </tr>
  <tr>
    <td>Dynamic Site Accelerator</td>
    <td>Site_Accel</td>
  </tr>
  <tr>
    <td>Rich Media Accelerator</td>
    <td>Rich_Media_Accel</td>
  </tr>
  <tr>
    <td>Web Application Accelerator</td>
    <td>Web_App_Accel</td>
  </tr>  
</table>

If you don’t see your product code, run this command to return the product IDs available for your account: 
`akamai pipeline list-products -c <contractId>`  
  
## Save and promote changes through the pipeline

Once you make changes to your pipeline, you can save and promote those changes to all environments in your pipeline. 

To save and promote changes:

1. Make your configuration change within the desired snippet inside your `templates` folder. This folder contains JSON snippets for the top-level rules in your property’s configuration. 

2. Optionally, update the property files to reflect your changes without saving to Property Manager: 
        akamai pipeline merge -p <pipelineName> <environment_name>

3. Save your changes, validate your configuration, and create a new property version: 
        akamai pipeline  save -p <pipelineName> <environment_name>

4. Promote the change to the first environment in your pipeline: 
        akamai pipeline  promote -p <pipelineName> -n <network> <environment_name> <notification_emails>. 

    The `<network>` value corresponds to Akamai’s staging and production networks. You can enter `STAGING` or `PROD` for this value. For example: 
        akamai pipeline  promote -p MyPipeline123 -n STAGING qa jsmith@example.com

    When run, `promote` merges the template and variables files, saves any changes to Property Manager, and 
    activates the property version on the selected Akamai network. 
    
5. Once the activation is complete, run the following command to make sure the pipeline reflects the latest activation status: 
        akamai pipeline  check-promotion-status <environment_name>

    **Note:** You should receive an email once activation is complete. Activation times vary, so you may want to wait several minutes before attempting to run this command.

6. Repeat steps 2 through 5 until you promote your changes to all environments in the pipeline. 

7. Verify that the updates made it to all environments in the pipeline: 
        akamai pipeline lstat -p <pipelineName>

# How you can use this CLI

Here are some ways you can use the Property Manager CLI to meet your business needs.

## Using Property Manager user variables with Akamai Pipeline

Some Property Manager behaviors, like Origin Server [`origin`](https://developer.akamai.com/api/core_features/property_manager/v2018-02-27.html#origin), let you define custom user variables for certain settings. 

If you have an existing property that includes user variables, Akamai Pipeline recognizes these variables and automatically populates the `variabledefinitions.json` and the environment-specific `variables.json` files to support these variables. 

As a best practice, review your property's custom variables in the Property Manager application before creating a new pipeline and adjust as needed. See the Property Manager help system for more information about setting user variables in the GUI application.

If you need to declare new user variables after you create your pipeline, you'll need to revise the `../environments/variableDefinitions.json` and `../environments/{environment}/variables.json` files using the [syntax for Property Manager API variables](https://developer.akamai.com/api/core_features/property_manager/v1.html#declaringvariables).

## Using attributes that vary across environments with Akamai Pipeline

Property Manager CLI is a client-only library. With it you can tokenize any attribute in your configuration and have different values for that attribute in each environment. 

Say you want a behavior enabled on one of your three environments for testing purposes. How can you set this up for your pipeline?

Many behaviors have a boolean `enabled` field, which is essentially an on/off switch. Here’s an example for the Forward Rewrite Cloudlet:

```
{
  "name": "forwardRewrite",
  "options": {
    "enabled": true,
    "cloudletPolicy": {
      "id": 12345,
      "name": "some policy name"
    }
  }
}
```

We need to change the value of `enabled` to a variable. We'll do this in a few steps:


1. Pick a variable name, like `forwardRewriteEnabled`, and add it to the `variableDefinitions.json` file:

    ```
    {
        "definitions": {
            "forwardRewriteEnabled": {
                "type": "boolean",
                "default": true
            },
    ...
       }
    }
    ```

1. In the `variables.json` file for each environment, add a new JSON variable with a valid value. In this example, the variable needs a boolean value.

    ```
    {
    "forwardRewriteEnabled": false,
    ...
    }
    ```

1. In the `templates` folder, update the JSON file containing the Forward Rewrite behavior by replacing the boolean value with the variable expression using the `env` prefix:

1. In the `templates` folder, open the JSON file containing the Forward Rewrite behavior.

1. Replace the boolean value with the variable expression using the `env` prefix, which represents the current environment:

    ```
    {
      "name": "forwardRewrite",
      "options": {
        "enabled": "${env.forwardRewriteEnabled}", //notice type string!
        "cloudletPolicy": {
          "id": 12345,
          "name": "some policy name"
        }
      }
    }
    ```
1. Merge, save, and promote your change as needed. 

## Reuse secure edge hostnames

When you first create a pipeline and run the `akamai pipeline save` command, it saves your pipeline and validates your pipeline’s configuration. In addition, this command creates edge hostnames for you, if the value of the  `edgeHostnameId` is null in the `hostnames.json` file. 

The Property Manager CLI can only create non-secure (HTTP) edge hostnames. If you want to use a secure (HTTPS) edge hostname with your pipeline, you can add an existing secure edge hostname to your `hostnames.json` file. You can also create a new one in Property Manager and add it to your `hostnames.json` file. Just be aware that it might take a while before the system sees the new secure edge hostname.

You can view a list of your existing available edge hostnames by using this command: 
    akamai property-manager list-edgehostnames -c <contractId> -g <groupId>
    
## Working with advanced behaviors

If you want to use a property configuration with advanced behaviors as a template, you need to convert all advanced behaviors to Custom Behaviors before creating the pipeline. See [Custom Behaviors for PAPI](https://developer.akamai.com/blog/2018/04/26/custom-behaviors-property-manager-papi) for more information.

# Notice

Your use of Akamai's products and services is subject to the terms and provisions outlined in [Akamai's legal policies](https://www.akamai.com/us/en/privacy-policies/).



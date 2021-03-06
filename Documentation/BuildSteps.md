# Build Steps Overview
Let's describe all the Build Steps that we have at our disposal

## List and describe all build steps
Here is a list of all build steps you have at your disposal when you use ALOps

### ALOps Tasks
- ALOps App Runtime Package
  * Get a NAV App runtime package for onprem deployment.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppRuntimePackage@1
          displayName: 'ALOps App Runtime Package'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            nav_serverinstance: BC140             # Specifies the name of a Business Central Server instance. $(nav_serverinstance)
            nav_tenant: default                   # Specifies the ID of a specific tenant that you want to act on, such as Tenant1. $(nav_tenant)
            targetproject: ./app.json             # Path of the project to export as RuntimePackage. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(targetproject)
            showmycode: False                     # Overwrites the ShowMyCode value in the manifest. $(showmycode)
            publish_artifact: True                # Publish generated Runtime-App Artifact to DevOps. $(publish_artifact)
    ```
- ALOps App Sign
  * Codesign Business Central extension with .pfx.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppSign@1
          displayName: 'ALOps App Sign'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            artifact_path:                        # Path for storing App Artifact. $(artifact_path)
            nav_artifact_app_filter: *.app        # Path of the App to Codesign. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(nav_artifact_app_filter)
            pfx_path:                             # Path or Url of the PFX file. $(pfx_path)
            timestamp_uri:                        # Uri of the timestamp service used during signing. $(timestamp_uri)
            publish_artifact: True                # Publish generated App Artifact to DevOps. $(publish_artifact)
            pfx_password:                         # Password for the PFX File. Recommended to use Azure-KeyVault secrets. $(pfx_password)
    ```
- ALOps App Sign Verify
  * Verify CodeSign of Business Central extension.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppSignVerify@1
          displayName: 'ALOps App Sign Verify'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            artifact_path:                        # Path for App Artifact. $(artifact_path)
            nav_artifact_app_filter: *.app        # Path of the App to verify. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(nav_artifact_app_filter)
    ```
- ALOps App Test
  * Run Business Central Test-Suite and collect results.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppTest@1
          displayName: 'ALOps App Test'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            bc_username:                          # BC Username for ServiceTier connection. $(bc_username)
            bc_password:                          # BC User Password for ServiceTier connection. $(bc_password)
            bc_authentication: NavUserPassword    # BC Authentication type of the ServiceTier. $(bc_authentication)
            bc_webclient_url:                     # BC WebClient Endpoint. $(bc_webclient_url)
            testfilter:                           # Filter codeunits to include in Test-Suite. $(testfilter)
            show_available_tests: True            # Show all available tests. $(show_available_tests)
            import_testtoolkit: True              # Import TestToolKit FOB. $(import_testtoolkit)
            import_action: Overwrite              # Import Action for importing Test-Suite FOB files. $(import_action)
            testpage: 130409                      # Set the PageId used for testing. $(testpage)
            testsuite: DEFAULT                    # Set target Test Suite to activate. $(testsuite)
            installaltesttool: False              # Install the AL TestTool for v15. $(installaltesttool)
            failed_test_action: Warning           # Action to take when a Test failed. $(failed_test_action)
    ```
- ALOps App Cleaner
  * Remove all extensions from Business Central service tier.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppClean@1
          displayName: 'ALOps App Cleaner'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            nav_computername: localhost           # Target Business Central Server running service tier. $(nav_computername)
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            sync_mode: Add                        # Mode for synchronizing Business Central extensions. $(sync_mode)
    ```
- ALOps App Copy
  * Copy Business Central extensions from one service tier to another.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppCopy@1
          displayName: 'ALOps App Copy'
          inputs:
            nav_computername: localhost           # Target Business Central Server running service tier. $(nav_computername)
            nav_serverinstance_source: BC140      # Source Business Central service tier to copy from. $(nav_serverinstance_source)
            nav_serverinstance_target: BC140      # Target Business Central service tier to copy to. $(nav_serverinstance_target)
            nav_app_storage:                      # Path used for temporary extension storage. $(nav_app_storage)
    ```
- ALOps App Compiler
  * Compile a Business Central extension from AL code.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppCompiler@1
          displayName: 'ALOps App Compiler'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            targetproject: ./app.json             # Path of the project to compile. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(targetproject)
            nav_computername: localhost           # Target Business Central Server running service tier. $(nav_computername)
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            nav_tenant: default                   # NAV Tenant used for symbols and compiling the app. $(nav_tenant)
            nav_ports_dev: 7049                   # NAV Port used for DEV-Service. $(nav_ports_dev)
            bc_username:                          # BC Username for ServiceTier connection. $(bc_username)
            bc_password:                          # BC User Password for ServiceTier connection. $(bc_password)
            al_compiler_version: 0.12.15355       # AL Compiler version used for compiling. $(al_compiler_version)
            al_analyzer:                          # AL Analyzer(s) used for compiling. (Example: CodeCop,UICop) $(al_analyzer)
            nav_app_version: 1.0.*.0              # Template for versioning NAV-Apps. '*' is replaced by the current Build Number. $(nav_app_version)
            vsix_download_path:                   # Alternative VSIX download url. $(vsix_download_path)
            use_ssl: False                        # Use SSL for Business Central connections. $(use_ssl)
            download_test_symbols: False          # Download Test Symbols explicitly (without Test-Version reference in App.json). $(download_test_symbols)
            usecompression: True                  # Compress Source-Folder for transfer to docker container. $(usecompression)
            publish_artifact: True                # Publish generated App Artifact to DevOps. $(publish_artifact)
            failed_on_warnings: False             # Fail task when any warning occurs. $(failed_on_warnings)
            app_file_suffix:                      # Set a suffix tag on the compiled App filename. $(app_file_suffix)
            updatebuildnumber: True               # Update the Build number with the current version. $(updatebuildnumber)
            setup_working_folder: False           # Copy working folder to Docker container. $(setup_working_folder)
    ```
- ALOps Docker Execute
  * Execute powershell script in container.
  * YAML Template: 
    ```yaml
        - task: ALOpsDockerExec@1
          displayName: 'ALOps Docker Execute'
          inputs:
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            scriptsource: InLine                  # Set type for requiring the script. $(scriptsource)
            script_location:                      # Location of the script to fetch. $(script_location)
            inline_script: |                      # Inline Powershell Script. $(inline_script)
              # Write your powershell commands here.
              Write-Host "Hello World"  
    ```
- ALOps Docker Remove
  * Remove Business Central docker container.
  * YAML Template: 
    ```yaml
        - task: ALOpsDockerRemove@1
          displayName: 'ALOps Docker Remove'
          inputs:
            usedocker: True                       # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            print_logs: True                      # Print all container logs before remove. $(print_logs)
    ```
- ALOps Docker Start
  * Start Business Central docker container.
  * YAML Template: 
    ```yaml
        - task: ALOpsDockerStart@1
          displayName: 'ALOps Docker Start'
          inputs:
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            ignore_no_container_warning: False    # Do not trigger warning when container with [fixed_tag] is not found. $(ignore_no_container_warning)
            docker_image: microsoft/bcsandbox     # Business Central docker Image to Start. $(docker_image)
            accept_image_eula: True               # Accept Eula of docker image. $(accept_image_eula)
            accept_image_outdated: False          # Accept Outdated image. $(accept_image_outdated)
            enable_symbol_loading: False          # Enable Symbol Loading. $(enable_symbol_loading)
            enable_api_services: False            # Enable API Services. $(enable_api_services)
            docker_pull: True                     # Force Pull docker image. $(docker_pull)
            dockerauthentication: None            # Set authentication Method to use. $(dockerauthentication)
            docker_login:                         # Select the generic login to use for docker. If needed, click on 'manage', and add a new Service Endpoint of type 'Generic' $(docker_login)
            docker_username:                      # Docker login username. $(docker_username)
            docker_password:                      # Docker login password. $(docker_password)
            docker_registry:                      # Docker registry for login, example: 'bcinsider.azurecr.io' $(docker_registry)
            memory_gb: -1                         # Set maximum memory for container in GB. $(memory_gb)
            container_restart: no                 # Set docker container restart preference. $(container_restart)
            docker_parameters: |                  # Specify additional docker parameters. $(docker_parameters)
              --isolation=hyperv
              --env ExitOnError=N
            sql_server:                           # External SQL Server. $(sql_server)
            sql_server_instance:                  # External SQL Server Instance. $(sql_server_instance)
            sql_database:                         # External SQL Database. $(sql_database)
            sql_database_user:                    # External SQL Database User. $(sql_database_user)
            sql_database_user_password:           # External SQL Database Password. $(sql_database_user_password)
            sql_backup_file:                      # Restore BAK file on startup. $(sql_backup_file)
            encryption_key:                       # Encryption key for Service Tier. $(encryption_key)
    ```
- ALOps Docker Wait
  * Wait until the Business Central container is started.
  * YAML Template: 
    ```yaml
        - task: ALOpsDockerWait@1
          displayName: 'ALOps Docker Wait'
          inputs:
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            search_string: Ready for connections! # String to match in Docker Logs and return. $(search_string)
            error_string:                         # Throw error when the container logs contain the error string. $(error_string)
            setup_working_folder: True            # Copy working folder to Docker container. $(setup_working_folder)
            usecompression: True                  # Compress Source-Folder for transfer to docker container. $(usecompression)
    ```
- ALOps Import FOB
  * Import objects from .FOB file.
  * YAML Template: 
    ```yaml
        - task: ALOpsFobImport@1
          displayName: 'ALOps Import FOB'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            filePath:                             # Path of the FOB to import. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(filePath)
            import_action: Default                # Import action for importing FOB files. $(import_action)
            synchronize_schema_changes: Yes       # Synchronize Schema Changes setting for importing FOB files. $(synchronize_schema_changes)
            generate_symbol_reference: False      # Enable Generate Symbol References. $(generate_symbol_reference)
    ```
- ALOps Info
  * Print information about ALOps and executing host.
  * YAML Template: 
    ```yaml
        - task: ALOpsInfo@1
          displayName: 'ALOps Info'
    ```
- ALOps License Import
  * Import Business Central license (.flf).
  * YAML Template: 
    ```yaml
        - task: ALOpsLicenseImport@1
          displayName: 'ALOps License Import'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            license_path:                         # Path of the FLF license to import. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory) or a downloadable Url. $(license_path)
            remove_license_file: True             # Remove license file after import. $(remove_license_file)
            print_license_info: False             # Set if License is printed into the pipeline. $(print_license_info)
            license_scope: Default                # Set the scope for license upload. $(license_scope)
    ```
- ALOps Package Import
  * Import and Process RapidStart/Configuration Package
  * YAML Template: 
    ```yaml
        - task: ALOpsPackageImport@1
          displayName: 'ALOps Package Import'
          inputs:
            usedocker: True                       # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            company_name:                         # Company name for Package import. $(company_name)
    ```
- ALOps App Publish
  * Publish Business Central extension to service tier.
  * YAML Template: 
    ```yaml
        - task: ALOpsAppPublish@1
          displayName: 'ALOps App Publish'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            installaltesttool: False              # Install the AL TestTool for v15. $(installaltesttool)
            install_al_app_names: |               # Specify additional docker parameters. $(install_al_app_names)
              Tests-TestLibraries
              System Application Test
              System Application Test Library
              Any
              Library Assert
              Test Runner 
            nav_serverinstance: BC140             # Business Central Server Instance Name. $(nav_serverinstance)
            artifact_path:                        # Path for storing App Artifact. $(artifact_path)
            nav_artifact_app_filter: *.app        # Filter used for locating App file relative to $(path_to_publish). $(nav_artifact_app_filter)
            skip_verification: True               # Skip CodeSign Verification of Business Central App. $(skip_verification)
            publish_scope: Global                 # Set the scope for publishing extensions. $(publish_scope)
            tenant: default                       # Tenant to publish to when Scope is set to Tenant. $(tenant)
            batch_publish_folder:                 # Path containing Apps to publish. $(batch_publish_folder)
    ```
- ALOps Repository Publish Extension
  * Publish extension to ALOps Repository.
  * YAML Template: 
    ```yaml
        - task: ALOpsRepositoryPublish@1
          displayName: 'ALOps Repository Publish Extension'
          inputs:
            usedocker: False                      # Run task in Docker container. $(usedocker)
            fixed_tag:                            # Allows recycling of docker containers. $(fixed_tag)
            alops_repository_endpoint: api.businesscentral.dynamics.com# Azure API Endpoint. $(alops_repository_endpoint)
            alops_repository_username:            # Username for ALOps API Endpoint. $(alops_repository_username)
            alops_repository_password:            # Password for ALOps API Endpoint. $(alops_repository_password)
            artifact_path:                        # Path for App Artifact. $(artifact_path)
            app_artifact_filter: *.app            # Path of the App to publish. Must be a fully qualified path or relative to $(System.DefaultWorkingDirectory). $(app_artifact_filter)
            upload_c_applications: False          # Upload Applications from Applications folder. $(upload_c_applications)
    ```
- ALOps SaaS Get Extensions
  * Get extensions from Business Central Saas.
  * YAML Template: 
    ```yaml
        - task: ALOpsSaaSGetExtensions@1
          displayName: 'ALOps SaaS Get Extensions'
          inputs:
            azure_api_endpoint: api.businesscentral.dynamics.com# Azure API Endpoint. $(azure_api_endpoint)
            azure_api_version: v2.0               # Azure API Endpoint version. $(azure_api_version)
            azure_tenant_id:                      # Azure Tenant Id. $(azure_tenant_id)
            azure_app_client_id:                  # Azure AD Application Client Id. $(azure_app_client_id)
            azure_app_client_secret:              # Azure AD Application Client Secret. $(azure_app_client_secret)
            bc_impersonate_user:                  # Business Central User to impersonate. $(bc_impersonate_user)
            bc_impersonate_password:              # Business Central User Password to impersonate. $(bc_impersonate_password)
            bc_environment: sandbox               # Business Central environment to publish extension on. $(bc_environment)
            bc_companyname:                       # Business Central Company. $(bc_companyname)
    ```
- ALOps SaaS Publish Extension
  * Publish extension to Business Central Saas.
  * YAML Template: 
    ```yaml
        - task: ALOpsSaaSPublishExtension@1
          displayName: 'ALOps SaaS Publish Extension'
          inputs:
            azure_api_endpoint: api.businesscentral.dynamics.com# Azure API Endpoint. $(azure_api_endpoint)
            azure_api_version: v2.0               # Azure API Endpoint version. $(azure_api_version)
            azure_tenant_id:                      # Azure Tenant Id. $(azure_tenant_id)
            azure_app_client_id:                  # Azure AD Application Client Id. $(azure_app_client_id)
            azure_app_client_secret:              # Azure AD Application Client Secret. $(azure_app_client_secret)
            bc_impersonate_user:                  # Business Central User to impersonate. $(bc_impersonate_user)
            bc_impersonate_password:              # Business Central User Password to impersonate. $(bc_impersonate_password)
            bc_environment: sandbox               # Business Central environment to publish extension on. $(bc_environment)
            bc_companyname:                       # Business Central Company. $(bc_companyname)
            artifact_path:                        # Path for App Artifact. $(artifact_path)
            app_artifact_filter: *.app            # Filter used for locating App file relative to $(path_to_publish). $(app_artifact_filter)
    ```


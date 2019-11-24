# docker-devenv

Publishes docker images for development environments and associated CMDs to PS gallery.

1. Powershell pipeline

   ```mermaid
   graph TB
   	subgraph git[Git]
   		subgraph git_devenv[docker-devenv]
   			subgraph git_devenv_config[deploy.yml]
   				git_devenv_config_ps[Powershell configuration]
   			end

   			git_devenv_bootstrap[Powershell bootstrap script]
   		end
   	end

   	subgraph pipeline[Pipeline]
   		pipeline_ps[Powershell]
   	end

   	subgraph ps[Powershell gallery]
   		ps_bootstrap[DockerDevenv]
   	end

   	subgraph pypi[PyPI]
   		pypi_package[deployment-automation]
   	end

   	%% Pipeline - Powershell
   	pipeline_ps -- Use --> git_devenv_config_ps -- Reference --> git_devenv_bootstrap
   	pipeline_ps == Publish ==> ps_bootstrap
   	pipeline_ps -- Use --> pypi_package
   ```

1. Docker pipeline

   ```mermaid
   graph TB
   	subgraph docker[Docker repository]
   		docker_image[ubuntu-devenv-ts]
   		docker_parent[ubuntu]
   	end

   	subgraph git[Git]
   		subgraph git_devenv[docker-devenv]
   			subgraph git_devenv_config[deploy.yml]
   				git_devenv_config_docker[Docker configuration]
   			end

   			git_devenv_template[Dockerfile template]
   		end
   	end

   	subgraph pipeline[Pipeline]
   		pipeline_docker[Docker]
   	end

   	subgraph ps[Powershell gallery]
   		ps_bootstrap[DockerDevenv]
   	end

   	subgraph pypi[PyPI]
   		pypi_package[deployment-automation]
   	end

   	%% Pipeline - Docker
   	git_devenv_template -- Reference --> docker_parent
   	pipeline_docker == Publish ==> docker_image
   	pipeline_docker -- Use --> git_devenv_config_docker -- Reference --> git_devenv_template -- CMD --> ps_bootstrap
   	pipeline_docker -- Use --> pypi_package
   ```

1. IDE pipeline

   ```mermaid
   graph TB
   	subgraph docker[Docker repository]
   		docker_image[ubuntu-devenv-ts]
   	end

   	subgraph git[Git]
   		subgraph git_awesome[ts-awesome]
   			git_awesome_ide[IDE bootstrap]
   		end

   		subgraph git_devenv[docker-devenv]
   			subgraph git_devenv_config[deploy.yml]
   				git_devenv_config_ide[IDE configuration]
   			end

   			git_devenv_ide[IDE bootstrap]
   		end
   	end

   	subgraph pipeline[Pipeline]
   		pipeline_ide[IDE bootstrap]
   	end

   	subgraph ps[Powershell gallery]
   		ps_bootstrap[DockerDevenv]
   	end

   	subgraph pypi[PyPI]
   		pypi_package[deployment-automation]
   	end

   	%% Pipeline - IDE
   	git_devenv_config_ide -- Reference --> git_devenv_ide
   	git_devenv_ide -- Reference --> docker_image
   	git_devenv_ide -- Reference --> ps_bootstrap
   	pipeline_ide == Push ==> git_awesome_ide
   	pipeline_ide -- Uses --> git_devenv_config_ide
   	pipeline_ide -- Use --> pypi_package
   ```

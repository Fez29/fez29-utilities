SHELL := /bin/zsh
OS := $(shell uname -s)

.PHONY: check_os install_docker install_tfenv install_terraform install_terragrunt install_gcloud_cli install_all

install_all: install_docker install_tfenv install_terraform install_terragrunt install_gcloud_cli
uninstall_all: uninstall_docker

check_os:
ifeq ($(OS),)
endif
	@echo "Detected OS: $(OS)"

install_docker: check_os
ifeq ($(OS),Linux)
	@echo "Checking if Docker is already installed..."
	@if ! command -v docker >/dev/null; then \
		echo "Installing Docker on Linux..."; \
		PKG_MGR=$(shell command -v apt || command -v dnf || command -v yum); \
		echo ${PKG_MGR}; \
		if [ -n "$${PKG_MGR}" ]; then \
			if [ "$${PKG_MGR}" = "/usr/bin/apt" ]; then \
				echo "PKG_MGR = apt"; \
				sudo apt update; \
				sudo apt install -y apt-transport-https ca-certificates curl software-properties-common; \
				curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg; \
				echo "deb [arch=$(shell dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null; \
				sudo apt update; \
				sudo apt install -y docker-ce docker-ce-cli containerd.io; \
			elif [ "$${PKG_MGR}" = "/usr/bin/dnf" ]; then \
				sudo dnf install -y dnf-plugins-core; \
				sudo dnf config-manager --add-repo=https://download.docker.com/linux/fedora/docker-ce.repo; \
				sudo dnf install -y docker-ce docker-ce-cli containerd.io; \
			elif [ "$${PKG_MGR}" = "/usr/bin/yum" ]; then \
				sudo yum install -y yum-utils; \
				sudo yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo; \
				sudo yum install -y docker-ce docker-ce-cli containerd.io; \
			fi; \
			echo "Adding current user to the docker group..."; \
			sudo groupadd -f docker; \
			sudo usermod -aG docker $(shell whoami); \
			newgrp docker; \
			echo "Docker installed and configured successfully."; \
		else \
			echo "Unsupported package manager. Please install Docker manually."; \
		fi; \
	else \
		echo "Docker is already installed."; \
	fi
else ifeq ($(OS),Darwin)
	@if ! command -v docker >/dev/null; then \
	@echo "Installing Docker on macOS..." \
	brew install --cask docker
else
	@echo "Unsupported OS. Please install Docker manually."
endif

install_tfenv: check_os
ifeq ($(OS),Linux)
	@echo "Checking if tfenv is already installed..."
	@if ! command -v tfenv >/dev/null; then \
		echo "Installing tfenv on Linux..."; \
		git clone https://github.com/tfutils/tfenv.git ~/.tfenv; \
		echo 'export PATH="$${HOME}/.tfenv/bin:$${PATH}"' >> ~/.zshrc; \
		source ~/.zshrc; \
	else \
		echo "tfenv is already installed."; \
	fi
else ifeq ($(OS),Darwin)
	@echo "Checking if tfenv is already installed..."
	@if ! command -v tfenv >/dev/null; then \
		echo "Installing tfenv on macOS..."; \
		brew install tfenv; \
	else \
		echo "tfenv is already installed."; \
	fi
else
	@echo "Unsupported OS. Please install tfenv manually."
endif

install_terraform: install_tfenv
	@echo "Installing the latest version of Terraform using tfenv..."
	tfenv install latest
	tfenv use latest

install_terragrunt: check_os
ifeq ($(OS),Linux)
	@echo "Installing Terragrunt on Linux..."
	curl -Ls https://github.com/gruntwork-io/terragrunt/releases/download/v0.37.0/terragrunt_linux_amd64 -o terragrunt
	chmod +x terragrunt
	sudo mv terragrunt /usr/local/bin/
else ifeq ($(OS),Darwin)
	@echo "Installing Terragrunt on macOS..."
	brew install terragrunt
else
	@echo "Unsupported OS. Please install Terragrunt manually."
endif

install_gcloud_cli: check_os
ifeq ($(OS),Linux)
	@echo "Installing Google Cloud CLI on Linux..."
	curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk.tar.gz
	tar -xvzf google-cloud-sdk.tar.gz
	./google-cloud-sdk/install.sh --quiet
	rm -f google-cloud-sdk.tar.gz
	echo 'export PATH="$${HOME}/google-cloud-sdk/bin:$${PATH}"' >> ~/.zshrc
	source ~/.zshrc
else ifeq ($(OS),Darwin)
	@echo "Installing Google Cloud CLI on macOS..."
	brew install --cask google-cloud-sdk
	echo 'source "/usr/local/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/path.zsh.inc"' >> ~/.zshrc
	source ~/.zshrc
else
	@echo "Unsupported OS. Please install Google Cloud CLI manually."
endif

uninstall_docker: check_os
ifeq ($(OS),Linux)
	@echo "Uninstalling Docker..."
	@PKG_MGR=$(shell command -v apt-get || command -v dnf || command -v yum); \
	if [ -n "$${PKG_MGR}" ]; then \
		sudo $${PKG_MGR} remove -y docker-ce docker-ce-cli containerd.io; \
	else \
		echo "Unsupported package manager. Please uninstall Docker manually."; \
		exit 1; \
	fi
	@echo "Removing Docker data and configuration files..."
	sudo rm -rf /var/lib/docker
	sudo rm -rf /var/lib/containerd
	sudo rm /etc/systemd/system/docker.service
	sudo rm /etc/systemd/system/docker.socket
	sudo systemctl daemon-reload
else ifeq ($(OS),Darwin)
	@echo "Uninstalling Docker on macOS using brew..."
	brew uninstall --cask docker
	@echo "Removing Docker data and configuration files..."
	rm -rf ~/Library/Containers/com.docker.docker
	rm -rf ~/Library/Group\ Containers/group.com.docker
	rm -rf ~/Library/Preferences/com.docker.docker.plist
	rm -rf ~/Library/Preferences/com.electron.docker.helper.plist
	rm -rf ~/Library/Application\ Scripts/com.docker.helper
	rm -rf ~/Library/Caches/com.docker.docker
	rm -rf ~/Library/Caches/com.plausiblelabs.crashreporter.data/com.docker.docker
	rm -rf ~/.docker
else
	@echo "Unsupported OS. Please install Google Cloud CLI manually."
endif

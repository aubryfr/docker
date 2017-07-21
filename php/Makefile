.PHONY: help pull build test tag images push

TARGETS:=$(MAKEFILE_LIST)
ITSELF:=$(lastword $(MAKEFILE_LIST))
MAKEFILE_PATH:=$(abspath $(lastword $(TARGETS)))
.DEFAULT_GOAL:=help

help: ## This help
	@grep -E '^[a-z A-Z_-]+:.*?## .*$$' $(TARGETS) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

include .env
.env:

FQIN=inextensodigital/${IMAGE_NAME}:${TAG}
LATEST=inextensodigital/${IMAGE_NAME}:latest

pull: ## Pull docker image
	@docker pull ${FQIN} > /dev/null 2>&1 || (echo "${FQIN} does not exist yet. Will build it." && echo "IMAGE_BUILT=true" >> .env)

build: ## Build docker image. Use f=true to force
	@if [ "$(shell docker image ls -q ${FQIN})" = "" ] || [ "${f}" = "true" ]; then \
		docker build --cache-from=inextensodigital/php:5.6.30-apache \
					 --build-arg FROM_FQIN=${FROM_FQIN} \
					 --build-arg COMPOSER_VERSION=${COMPOSER_VERSION} \
					 --build-arg PRESTISSIMO_VERSION=${PRESTISSIMO_VERSION} \
					 --build-arg APCU_PACKAGE=${APCU_PACKAGE} \
					 --build-arg REDIS_VERSION=${REDIS_VERSION} \
					 --build-arg MEMCACHED_VERSION=${MEMCACHED_VERSION} \
					 --build-arg IMAGICK_VERSION=${IMAGICK_VERSION} \
					-t ${FQIN} .; \
	fi

test: ## Test built docker image
	#docker run --rm ${FQIN} php --version;
	#docker run --rm ${FQIN} php -m;

tag: ## Make a tag alias latest to the built image
	@docker tag ${FQIN} ${LATEST}

images: ## List built images
	docker image ls inextensodigital/${IMAGE_NAME}

push: ## Push built images
	@if [ "$(IMAGE_BUILT)" = "true" ]; then \
		docker login --username="$(DOCKER_HUB_USERNAME)" --password="$(DOCKER_HUB_PASSWORD)"; \
		docker push ${FQIN}; \
		docker push ${LATEST}; \
	fi

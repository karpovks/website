DOCKER       = docker
HUGO_VERSION = 0.83.1
DOCKER_IMAGE = personal-hugo-universal
DOCKER_IMAGE_ID = $(shell docker images -q $(DOCKER_IMAGE):latest)
DOCKER_RUN   = $(DOCKER) run --rm --interactive --tty --volume $(PWD):/src
HUGO_LOCAL_OUTPUT_DIR = 'docs-local/'

.PHONY: all clean

# If the first argument is "new-module"...
ifneq (,$(findstring new-module,$(firstword $(MAKECMDGOALS))))
  # use the rest as arguments for "new-module"
  NEW_MODULE_ARGS := $(wordlist 2,$(words $(MAKECMDGOALS)),$(MAKECMDGOALS))
  # ...and turn them into do-nothing targets
  $(eval $(NEW_MODULE_ARGS):;@:)
endif


HUGO_BUILD_CMD = server -d $(HUGO_LOCAL_OUTPUT_DIR)
HUGO_SERVE_CMD = server -D --watch --bind 0.0.0.0 -d $(HUGO_LOCAL_OUTPUT_DIR)
HUGO_NEW_MODULE = new --kind topic content/$(NEW_MODULE_ARGS)


# Generic
#-------------------------------------------------------------------------------
help:                  ## Show this help
	@echo "\nIf you have docker installed, use the following targets (make docker-serve is probably what you want for local development): \n"
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e 's/\\$$//' | sed -e 's/##//'
	@echo "\n"
	@echo "Don't forget to checkout contributions.md for more info on how to contribute to this repo."
	@echo "\n"

# target vs _target naming convention: https://3musketeers.io/docs/make.html#target-vs-target

# targets that need specific environment/dependencies
#-------------------------------------------------------------------------------

_clean:                ## (via shell) Remove the docs-local/ folder from your system
	rm -rf $(HUGO_LOCAL_OUTPUT_DIR)

_build:                ## (via hugo) Build site with production settings
	$(call HUGO_BUILD_CMD)

_serve:                ## (via hugo) Boot the development server
	$(call HUGO_SERVE_CMD)

_new-module:           ## (via hugo) Create new module
	$(call HUGO_NEW_MODULE)

# can be called on any platform (Windows, Linux, MacOS)
#-------------------------------------------------------------------------------

build-github-pages:            ## (via docker) Build hugo site (prod) - used by the CI/CD tool
	docker-compose run --rm build-prod

build-personaal:            ## (via docker) Build hugo site (prod) - used to output docs that work in discover github pages
	 rm -rf docs && docker-compose run --rm build-personal

serve:                 ## (via docker) Run hugo server locally
	docker-compose up serve-site

shell:                 ## (via docker) Start a shell where you can run adhoc/interactive commands in a hugo environment
	docker-compose run --rm hugo shell

new-module:            ## (via docker) Create a new lesson (make docker-new-module foo/bar) see https://miniature-parakeet-41bab0fd.pages.github.io/dojos/contributions/#how-to-create-a-new-lesson-module
	docker-compose run --rm hugo $(HUGO_NEW_MODULE)

clean:                 ## (via docker) Deletes files as root without needing sudo, useful for linux users where files are created as root by docker container so sudo isn't needed
	docker-compose run --rm utility rm -rf /src/$(HUGO_LOCAL_OUTPUT_DIR)

GITIGNORE_TARGETS ?= python,golang

## Update submodules
git/submodules-update:
	$(call assert-set,GIT)
	$(GIT) submodule update --init --remote

## Update git aliases
git/aliases-update:
	@$(GIT) config --global --add alias.permission-reset '!git diff -p -R --no-color | grep -E "^(diff|(old|new) mode)" --color=never | git apply'

## Export git vars
git/export:
	$(call print-var,GIT_COMMIT)
	$(call print-var,GIT_COMMIT_SHORT)
	$(call print-var,GIT_COMMIT_TIMESTAMP)
	$(call print-var,GIT_COMMIT_URL)
	$(call print-var,GIT_COMMIT_MESSAGE)
	$(call print-var,GIT_COMMIT_AUTHOR)
	$(call print-var,GIT_BRANCH)
	$(call print-var,GIT_TAG)
	$(call print-var,GIT_LATEST_TAG)
	$(call print-var,GIT_LATEST_TAG_COMMIT)
	$(call print-var,GIT_LATEST_SEMVER_TAG)
	$(call print-var,GIT_BRANCH_TAG)
	$(call print-var,GIT_IS_BRANCH)
	$(call print-var,GIT_IS_TAG)

## Build .gitignore file if one doesn't exist
git/ignore:
	$(call assert-set,GITIGNORE_TARGETS)
	@if [[ ! -f ".gitignore" ]]; then\
		printf "Generating .gitignore file using https://www.gitignore.io";\
		wget "https://www.gitignore.io/api/$(GITIGNORE_TARGETS)" -O .gitignore;\
		if [[ -f ".gitignore.template" ]]; then\
			printf "\n.build-harness\n" >> .gitignore;\
			printf "\n# From .gitignore.template\n" >> .gitignore;\
			cat .gitignore.template >> .gitignore;\
		fi;\
	else\
		echo "You already have a .gitignore file.";\
	fi

.gitignore: git/ignore
	@exit 0


git/require_master:
	@if [ $$(git rev-parse --abbrev-ref HEAD) != "master" ]; then\
		echo "You are not on master.";\
		exit 0;\
	fi

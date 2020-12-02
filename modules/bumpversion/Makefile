BUMPVERSION=$(call which,advbumpversion)
BV_VERSION=$(shell test -f .bumpversion.cfg && awk '/current_version[ ]*=[ ]*([0-9\.]*)/' .bumpversion.cfg | awk 'match($$0, /[0-9\.]+/) {print substr($$0, RSTART, RLENGTH)}')

CHANGELOG_PATH ?= CHANGELOG.md

define bv-dry-run
$(shell $(BUMPVERSION) $(1) --dry-run --list --allow-dirty | awk '/new_version[ ]*=[ ]*([0-9\.]*)/' | awk 'match($$0, /[0-9\.]+/) {print substr($$0, RSTART, RLENGTH)}')
endef

.PHONY: bumpversion/debug
bumpversion/debug: bh/venv
	$(call print-var,BUMPVERSION)
	$(call print-var,BV_VERSION)

.PHONY: bumpversion/check
bumpversion/check:
	$(call assert-set,BUMPVERSION)

.PHONY: bumpversion/patch
## Increment the patch version number (i.e. 1.0.0 -> 1.0.1)
bumpversion/patch: git/require_master bh/venv bumpversion/check changelog/exists/patch
	$(BUMPVERSION) patch --verbose
	$(GIT) push --follow-tags

.PHONY: bumpversion/minor
## Increment the minor version number (i.e. 1.0.0 -> 1.1.0)
bumpversion/minor: git/require_master bh/venv bumpversion/check changelog/exists/minor
	$(BUMPVERSION) minor --verbose
	$(GIT) push --follow-tags

.PHONY: bumpversion/major
## Increment the major version number (i.e. 1.0.0 -> 2.0.0)
bumpversion/major: git/require_master bh/venv bumpversion/check changelog/exists/major
	$(BUMPVERSION) major --verbose
	$(GIT) push --follow-tags


.PHONY: bumpversion/release_patch
bumpversion/release_patch: bumpversion/patch

.PHONY: bumpversion/release_minor
bumpversion/release_minor: bumpversion/minor

.PHONY: bumpversion/release_major
bumpversion/release_major: bumpversion/major

define semver-part
$(shell echo "$(1)" | sed -E 's/^v?([0-9]*)[.]([0-9]*)[.]([0-9]*)$$/\$(2)/')
endef

define semver-prefix
$(shell echo "$(1)" | sed -E 's/^(v?)[0-9.]*$$/\1/')
endef

ifneq ($(GIT_LATEST_SEMVER_TAG),)
SEMVER_PREFIX:=$(call semver-prefix,$(GIT_LATEST_SEMVER_TAG))
SEMVER_MAJOR:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),1)
SEMVER_MINOR:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),2)
SEMVER_PATCH:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),3)
else
SEMVER_PREFIX?=
SEMVER_MAJOR?=0
SEMVER_MINOR?=0
SEMVER_PATCH?=0
endif

.PHONY: semver/export
semver/export:
	$(call print-var,GIT_LATEST_SEMVER_TAG)
	$(call print-var,SEMVER_PREFIX)
	$(call print-var,SEMVER_MAJOR)
	$(call print-var,SEMVER_MINOR)
	$(call print-var,SEMVER_PATCH)

define semver
$(shell echo $(SEMVER_PREFIX)$(if $(filter major,$(1)),$$(($(SEMVER_MAJOR) + 1)).0.0,)$(if $(filter minor,$(1)),$(SEMVER_MAJOR).$$(($(SEMVER_MINOR) + 1)).0,)$(if $(filter patch,$(1)),$(SEMVER_MAJOR).$(SEMVER_MINOR).$$(($(SEMVER_PATCH) + 1)),))
endef

define semver-diff
$(shell echo "$(GIT_LATEST_SEMVER_TAG) -> $(call semver,$(1))")
endef

define semver-tag
$(call git-tag,$(call semver,$(1)),$(call semver-diff,$(1)))
endef

# exit $(if $$(echo "major minor patch" | grep -w "$(1)"),0,1)
define semver-validate
if [ -z "$$(echo "major minor patch" | grep -w $(1))" ]; then echo "Invalid input."; exit 1; fi
endef

define repo-name
$(shell echo "$$(basename -s .git `$(GIT) config --get remote.origin.url`)")
endef

.PHONY: semver/check
semver/check:
	$(call assert-set,GIT_LATEST_SEMVER_TAG)

.PHONY: semver
semver: semver/check
	@echo -e "$(call green,$(call bold,Project)): $(call repo-name)"
	@echo -e "$(call green,$(call bold,Version)): $(GIT_LATEST_SEMVER_TAG)"

.PHONY: version
version: semver

.PHONY: semver/announce/%
semver/announce/%: semver/check
	@echo -e "$(call green,Project): $(call repo-name)"
	@echo -e "$(call green,Version): $(GIT_LATEST_SEMVER_TAG) -> $(call yellow,$(call semver,$*))"

.PHONY: semver/validate/%
semver/validate/%:
	@$(call semver-validate,$*)

.PHONY: semver/%
semver/%: git/require_master semver/check
	@$(MAKE) semver/validate/$* semver/announce/$* changelog/exists/$*
	@$(call semver-tag,$*)

.PHONY: release/%
release/%:
	@$(MAKE) semver/$*

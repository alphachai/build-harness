SEMVER_REGEX:=([0-9]*)[.]([0-9]*)[.]([0-9]*)
define semver-part
$(shell echo "$(1)" | sed -E 's/$(SEMVER_REGEX)/\$(2)/' )
endef

ifneq ($(GIT_LATEST_SEMVER_TAG),)
SEMVER_MAJOR:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),1)
SEMVER_MINOR:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),2)
SEMVER_PATCH:=$(call semver-part,$(GIT_LATEST_SEMVER_TAG),3)
else
SEMVER_MAJOR?=0
SEMVER_MINOR?=0
SEMVER_PATCH?=0
endif

semver/export:
	$(call print-var,GIT_LATEST_SEMVER_TAG)
	$(call print-var,SEMVER_MAJOR)
	$(call print-var,SEMVER_MINOR)
	$(call print-var,SEMVER_PATCH)

define semver
$(shell echo $(if $(filter major,$(1)),$$(($(SEMVER_MAJOR) + 1)).0.0,)$(if $(filter minor,$(1)),$(SEMVER_MAJOR).$$(($(SEMVER_MINOR) + 1)).0,)$(if $(filter patch,$(1)),$(SEMVER_MAJOR).$(SEMVER_MINOR).$$(($(SEMVER_PATCH) + 1)),))
endef

semver:
	@echo "$(shell basename -s .git `$(GIT) config --get remote.origin.url`) $(GIT_LATEST_SEMVER_TAG)"

semver/%: git/require_master
	@echo "Bumping the $* version $(GIT_LATEST_SEMVER_TAG) -> $(call semver,$*)"
	@$(GIT) tag -a $(call semver,$*) -m "bump version $(GIT_LATEST_SEMVER_TAG) -> $(call semver,$*)"
	@$(GIT) push --follow-tags
define changelog-contains
if [ -n "$$(awk '/^## \[$1/' $(CHANGELOG_PATH))" ]; then exit 0; else echo "[$1] not found in $(CHANGELOG_PATH)"; exit 1; fi
endef

define changelog-contains-unreleased
if [ "$$(awk '/^## \[U/,/^## \[[0-9]/' $(CHANGELOG_PATH) |  awk '!/^## \[/' | grep -c .)" -gt 0 ]; then exit 0; else echo "Unreleased changes not found in $(CHANGELOG_PATH)"; exit 1; fi
endef

define changelog-exists
@if [ -f $(CHANGELOG_PATH) ]; then $(call changelog-contains,$(call semver,$(1))); else echo "Skipping changelog check because $(CHANGELOG_PATH) does not exist in $(CURDIR)"; fi
endef

.PHONY: changelog/exists/%
## Check that a changelog file exists and contains the new version.
changelog/exists/%:
	$(call changelog-exists,$*)

.PHONY: changelog/release/%
## Check the changelog for Unreleased notes and promotes to the new version.
changelog/release/%:
	@$(call changelog-contains-unreleased)
	@if [ -f $(CHANGELOG_PATH) ]; then perl -i -pe"s/## \[Unreleased\]/## \[Unreleased\]\n\n\n## \[$(call semver,$*)\] - $$(date +%Y-%m-%d)/g" $(CHANGELOG_PATH); fi
	@echo "Promoted unreleased to '$(call semver,$*)' ($(CHANGELOG_PATH))"
	git commit $(CHANGELOG_PATH) -m "Add CHANGELOG for $(call semver,$*)"
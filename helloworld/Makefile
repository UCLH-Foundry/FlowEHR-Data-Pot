#  Copyright (c) University College London Hospitals NHS Foundation Trust
#
#  Licensed under the Apache License, Version 2.0 (the "License");
#  you may not use this file except in compliance with the License.
#  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.
.PHONY: help

help: ## Show this help
	@echo
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) \
		| awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%s\033[0m|%s\n", $$1, $$2}' \
        | column -t -s '|'
	@echo

install-test: ## Installs the required dependencies for testing
	python3 -m pip install -e ".[test]"

install-build: ## Installs the required dependencies for building
	python3 -m pip install -e ".[build]"

install-lint: ## Installs the required dependencies for linting
	python3 -m pip install -e ".[lint]"

build: install-build ## Build the Python wheel(s)
	# SOURCE_DATE_EPOCH is needed to build the wheel idempotently
	SOURCE_DATE_EPOCH=315532800 python3 -m build

test: install-test ## Run Python unit tests
	pytest src/

artifacts: build ## Gather artifacts for sending to Spark
	mkdir -p artifacts
	cp src/helloworld/entrypoint.py artifacts
	cp dist/*.whl artifacts/

clean: ## Clean all built objects
	rm -rf artifacts
	rm -rf dist
	rm -rf src/*.egg-info
	find . -name "*.pyc" -type f -delete

mypy: install-test install-lint ## Run mypy (see https://jaredkhan.com/blog/mypy-pre-commit on why it isn't run in pre-commit hook)
	mypy "src"

lint: install-lint ## Run pre-commit checks
	pre-commit run --all-files

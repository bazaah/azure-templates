# Minimal Azure Pipelines Templates

This repo is personal template archive, read the top line comments of the .yml files for descriptions

## Example azure-pipelines.yml

- #### Rust

      variables:
        rust_version: stable
        check_all_features: false
        allow_clippy_fail: true
        test_cross_platform: true

      stages:
        - stage: compilation_check
          displayName: Compilation check
          jobs:
            - template: rust/check.yml@templates
              parameters: 
                rust: ${{ variables.rust_version }}
                all_features: ${{ variables.check_all_features }}
        - stage: clippy_check
          displayName: Clippy check
          jobs:
            - template: rust/clippy.yml@templates
              parameters:
                rust: ${{ variables.rust_version }}
                allow_fail: ${{ variables.allow_clippy_fail }}
        - stage: cargo_testing
          displayName: Cargo test(s)
          jobs:
            - template: rust/test.yml@templates
              parameters:
                rust: ${{ variables.rust_version }}
                id: '1'
                cross: ${{ variables.test_cross_platform }}
                allow_fail: false
                test_list:
                  - "--all"
                  - "--ignored"
            - template: rust/test.yml@templates
              parameters:
                rust: nightly
                id: '2'
                cross: ${{ variables.test_cross_platform }}
                allow_fail: false
                test_list:
                  - "--all"

      resources:
        repositories:
          - repository: templates
            type: github
            name: bazaah/azure-templates
            endpoint: YOUR-ENDPOINT-HERE

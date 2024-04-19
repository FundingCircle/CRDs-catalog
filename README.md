# CRDs-catalog

For those CRDs not in https://github.com/datreeio/CRDs-catalog.

## Usage

To add a new CRD see https://github.com/yannh/kubeconform?tab=readme-ov-file#customresourcedefinition-crd-support.

Sample github action steps:

```yaml
- name: Set up kube tools
  uses: yokawasa/action-setup-kube-tools@v0.9.3
  with:
    kubeconform: 0.6.4

- name: Validate helm chart
  shell: bash
  run: |
    PARAMS=(
      "-schema-location"
      "default"
      "-schema-location"
      "https://raw.githubusercontent.com/datreeio/CRDs-catalog/main/{{.Group}}/{{.ResourceKind}}_{{.ResourceAPIVersion}}.json"
      "-schema-location"
      "https://raw.githubusercontent.com/FundingCircle/CRDs-catalog/main/{{.Group}}/{{.ResourceKind}}_{{.ResourceAPIVersion}}.json"
      "-verbose"
      "-strict"
    )
    cd chart
    for file in $(ls -1 values-*.yaml); do
      env=${file#*-}
      env=${env%.*}
      echo "validating ${env}..."
      helm template -f ${file} . 2>/dev/null | kubeconform "${PARAMS[@]}"
    done
```

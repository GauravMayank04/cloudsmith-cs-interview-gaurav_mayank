Cloudsmith Support Assessment - Issues Found
=============================================

Issue 1: Missing Build Command in `build_package.yml`

File: `build_package.yml`  
Problem: The "Build package" step is missing the actual run command to build the Python package.

Fix done : Add the build command:
```yaml
- name: Build package
  run: python -m build
```

Issue 2: Missing OIDC Authentication in `release_package.yml`

File: `release_package.yml`  
Problem: The workflow attempts to use Cloudsmith CLI without proper OIDC authentication setup, but there's no step to install and configure the Cloudsmith CLI with OIDC.

Fix: Add the Cloudsmith CLI installation step with OIDC authentication:
```yaml
- name: Install Cloudsmith CLI
  uses: cloudsmith-io/cloudsmith-cli-action@v1.0.1
  with:
    oidc-namespace: ${{ env.CLOUDSMITH_NAMESPACE }}
    oidc-service-slug: ${{ env.CLOUDSMITH_SERVICE_SLUG }}
```

Issue 3: Missing `id-token: write` Permission in `release_package.yml`

File: `release_package.yml`  
Problem: The workflow needs `id-token: write` permission for OIDC authentication with Cloudsmith, but it's not included in the permissions section.

Fix: Add the missing permission:
```yaml
permissions:
  contents: read
  actions: read
  id-token: write
```

Issue 4: Swapped Repository Names in `promote_package.yml`

File: `promote_package.yml`  
Problem: The staging and production repository names are swapped in the environment variables.

Fix: Correct the repository assignments:
```yaml
CLOUDSMITH_STAGING_REPO: 'gaurav-repo' #staging repo created by me
CLOUDSMITH_PRODUCTION_REPO: 'production'
```

Issue 5: Unused Output Variable in `build_package.yml`
File: `build_package.yml`  
Problem:The build job defines an output variable `artifact_name` that is never used by any downstream workflows or jobs.

Fix: remove the unused output 
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

Issue 6: Missing Repository Reference in Promote Command
File: `promote_package.yml`  
Problem: The cloudsmith mv command is missing the full destination repository path - it should include the namespace.

Fix: Include the full destination path:
```yaml
cloudsmith mv --yes \
  ${{ env.CLOUDSMITH_NAMESPACE }}/${{ env.CLOUDSMITH_STAGING_REPO }}/$IDENTIFIER \
  ${{ env.CLOUDSMITH_NAMESPACE }}/${{ env.CLOUDSMITH_PRODUCTION_REPO }}
```


NOTES:-

1> I have added project version and Path dynamically to fetch from init.py file.

[project]
name = "example_package"
dynamic = ["version"]

2> [tool.hatch.version]
path = "src/example_package/__init__.py"
 
3> My staging repo name is - "gaurav-repo" and My production repo name is - "production"

What is this project ?
----------------------

This project is **NOT** the official libffi repository.

It is a fork of libffi sources hosted at https://github.com/libffi/libffi.

It is used as staging area to maintain and test patches that will be contributed back to the
official repository.


What is the branch naming convention ?
--------------------------------------

Each branch is named following the pattern `libffi-cmake-buildsystem-vX.Y.Z-YYYY-MM-DD-SHA{N}`

where:

* `vX.Y.Z` is the version of the forked project
* `YYYY-MM-DD` is the date of the last official commit associated with the branch.
* `SHA{N}` are the first N characters of the last official commit associated with the branch.


How to backport changes to a specific branch ?
----------------------------------------------

1. Fork `python-cmake-buildsystem/libffi`

2. Checkout the relevant `libffi-cmake-buildsystem-vX.Y.Z-YYYY-MM-DD-SHA{N}` branch.

3. Add the remote from which to cherry-pick commit from

4. Cherry-pick the commit(s)

5. Amend the commit updating title and message to include `[backport MR-1234]` and `Cherry-picked commit ...` information.

    Adding these details streamlines the creation of new `libffi-cmake-buildsystem-` branch allowing to easily identify which commits are specific to this fork and which ones have been backported.

    ```
    [Backport PR-7480] Add cmake configuration files
        
    See https://github.com/libffi/libffi/pull/535/commits/92080f42b9328b56469a2e1b9895d535dd23a2e7

    ...
    ```

    Or if the change has already been integrated into the upstream repository:

    ```
    [backport]  Fix struct arg passing

    Cherry-picked commit 86d5ecc5e from main libffi repository.

    ...
    ```

6. Finally, create a pull request against the relevant `libffi-cmake-buildsystem-vX.Y.Z-YYYY-MM-DD-SHA{N}` branch.


_Note: If you have push access to the `python-cmake-buildsystem/libffi` fork, you could directly push commit to the relevant branch_


How to update the version of libffi ?
-------------------------------------

1. Clone this repository and add a remote to the official project

```
git clone git://github.com/python-cmake-buildsystem/libffi
cd libffi
git remote add upstream git://github.com/libffi/libffi
git fetch upstream
```

2. Create a new branch following the convention

```
# Extract version from https://github.com/libffi/libffi/blob/master/configure.ac
XYZ=$(cat configure.ac  | grep AC_INIT | sed -re "s/AC_INIT\(\[libffi\], \[([0-9].[0-9](.[0-9]|\-rc[0-9]))\].+/\\1/")
echo "XYZ [${XYZ}]"

DATE=$(git show -s --format=%ci upstream/master | cut -d" " -f1)
echo "DATE [${DATE}]"

libffiSHA=$(git show -s --format=%h upstream/master)
echo "libffiSHA: [${libffiSHA}]"

BRANCH_NAME=libffi-cmake-buildsystem-v${XYZ}-${DATE}-${libffiSHA}
echo "BRANCH_NAME [${BRANCH_NAME}]"

git checkout -b ${BRANCH_NAME} ${libffiSHA}
```

3. Cherry-pick the specific commits from last `libffi-cmake-buildsystem-` branch. Resolve conflict as needed.

   - the commit hash included in each branch name is the libffi *upstream* hash, so
     this fork specific commits may be viewed with the git range (`..`) operation:
```
          git log --pretty=format:"%h" {libffiSHA}..libffi-cmake-buildsystem-xyz-{libffiSHA}
```

4. To **test the changes**, locally rebuild the project.

5. Publish the branch. (directly in this repo if you have push rights, or on a fork)


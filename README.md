# conda-migrate
How to migrate conda/anaconda/miniconda (far more than just a `mv`!)

```
export OLD_PATH="/old/anaconda"
export NEW_PATH="/new/anaconda"
# escape / because it is used in `sed`
export OLD_PATH_ESCAPED="\/old\/anaconda"
export NEW_PATH_ESCAPED="\/new\/anaconda"

# 0. just move the whole directory 
mv "$OLD_PATH" "$NEW_PATH"

# 1. change the path in the shell configuration file
# e.g. if you are using zsh
# . "$OLD_PATH/etc/profile.d/conda.sh" -> . "$NEW_PATH/etc/profile.d/conda.sh"

# optional : use GNU sed
# `brew install gnu-sed`
# `alias sed=gsed`
# if you are using the builtin `sed` of macos, the syntax below is different

# the scripts in conda environments has hard-coded interpreter path at their first lines. 
# Fix them with new path.
function replace()
{
    CURRENT="$PWD"
    cd "$1"
    for x in ./*; do file "$x" | grep text && sed -i 's/$OLD_PATH_ESCAPED/$NEW_PATH_ESCAPED/g' "$x" ; done
    cd "$CURRENT"
}

cd "$NEW_PATH"

# 2. change the interpreter path in conda base environment
replace bin

# 3. change the interpreter path in other conda environments
for x in envs/*; do replace "$x"; done
```

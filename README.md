
## Adjusted Prettier Opinionated Code Formatter

See original prettier source code (v3.4.2) - https://github.com/prettier/prettier

> ---
> ### This package provides only formatting for Vue files template section! 
> ---

#### PRETTIER CONFIG
```json
{
    "tabWidth": 4,
    "printWidth": 120,
    "semi": false,
    "singleQuote": true,
    "quoteProps": "consistent",
    "bracketSameLine": true,
    "htmlWhitespaceSensitivity": "ignore",
    "vueIndentScriptAndStyle": true,
    "singleAttributePerLine": true
}
```

#### INSTALLATION
Install package as dev dependency
```sh
npm install -D zero-code-formatter
```

#### HOW TO FORMAT A FILE
Run formatter from CLI with following command:
```sh
npx zero-code-formatter --write [pathToFile]
```

#### GIT POST-COMMIT HOOK
```sh
#!/bin/bash

# Get the list of .vue files changed in the last commit
LAST_COMMIT_FILES=$(git diff-tree --no-commit-id --name-only -r HEAD | grep ".vue$")

# Check if there are any Vue files in the last commit
if [[ -z "$LAST_COMMIT_FILES" ]]; then
    echo "No Vue files were changed in the last commit. Skipping Prettier formatting."
    exit 0
fi

echo "Formatting last commit's Vue files with Prettier..."
echo "$LAST_COMMIT_FILES"

# Run Prettier on each changed Vue file
for file in $LAST_COMMIT_FILES; do
    # Perform formatting twice because attributes are formatted after children, thus children might not have empty line before parent opening tag end
    for i in {1..2}; do
        npx zero-code-formatter --write --ignore-unknown "$file"

        if [[ $? -ne 0 ]]; then
            echo "Prettier failed to format $file. Aborting commit."
            exit 1
        fi
    done
done

# Check if Prettier made changes
git diff --quiet --exit-code $LAST_COMMIT_FILES
if [[ $? -eq 0 ]]; then
    echo "No formatting changes detected. Skipping creating new commit with formatted files."
    exit 0
fi

# Stage and commit formatted files
git add $LAST_COMMIT_FILES
git commit -m "FORMATTING: applied Prettier formatting to last commit's Vue files"

echo "Prettier formatting applied and committed!"
exit 0
```
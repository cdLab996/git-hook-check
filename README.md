# 🚀 git-hook-check

English | [简体中文](./README-zh_CN.md)

- Use git hook to block keywords for files in the staging area. [Link](./script/check-keyword.sh)
- Keyword blocking for configuration directory files. [Link](./script/file-check-keyword.sh)

## ✨ TODO

- [x] 🔨 Set the keywords to be filtered
- [x] 🔨 Set the path to a directory or file that does not need to be detected
- [ ] 🔨 xxx

## ⚡ code

```sh
# Pre commit hooks to keyword intercept the list of files in the staging area
# Add unwanted code to FILTER_WORDS if necessary

#!/bin/bash

# Set the filter keywords (support Chinese)
FILTER_WORDS="debugger 测试"

# Set the directories or file paths that don't need to be checked
IGNORE_PATHS=".git node_modules script src/App.vue README.md README-zh_CN.md"

# Get the list of files in the cache area
FILES=$(git diff --name-only --cached)

# Define if error flags are found
has_error=false

# Loop through the file list
for FILE in $FILES; do
  # Check if the file is in the directories or file paths that don't need to be checked
  IGNORE=false
  for IGNORE_PATH in $IGNORE_PATHS; do
    if [[ $FILE == *"$IGNORE_PATH"* ]]; then
      IGNORE=true
      break
    fi
  done
  if $IGNORE; then
    continue
  fi

  # Check if the file contains the filter keywords
  for FILTER_WORD in $FILTER_WORDS; do
    if grep -Eiq "$FILTER_WORD" "$FILE"; then
      echo -e "\033[31m[Warning]\033[0m Keyword \033[31m$FILTER_WORD\033[0m found in file $FILE"
      has_error=true
    fi
  done
done

# If an error is found, exit
if $has_error; then
  exit 1
fi

echo -e "\033[32m[Info]\033[0m No filtered keywords found"
exit 0

```

## 🔍 Execution Effect

### fail

![fail](./screenshots/fail.png)

### succeed

![succeed](./screenshots/succeed.png)

## 🎈 License

[![GitHub license](https://img.shields.io/github/license/WuChenDi/git-hook-check)](https://github.com/WuChenDi/git-hook-check/blob/master/LICENSE)

<!-- npx husky add .husky/pre-commit "npm run check-commit"
https://juejin.cn/post/6908534290179424263
https://stackoverflow.com/questions/13877469/how-to-disable-git-push-when-there-are-todos-in-code
https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks#Client-Side-Hooks
http://mark-story.com/posts/view/using-git-commit-hooks-to-prevent-stupid-mistakes -->

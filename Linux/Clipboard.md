---
description: Interact with the clipboard using `xclip`
tags: Linux
tags: tutorial
---
# Prerequisite
Install `xclip`.

# Setup
## Copy to Clipboard
This will copy `"text"` to the clipboard:
```sh
echo "text" | xclip -in
```

## Paste from Clipboard
This will output the text you copied
```sh
xclip -out
```

# Bonus
## Implement Your Own
Create a file in `~/.cache` called `clipboard`
```sh
touch ~/.cache/clipboard
```

Use bash functions to interact with the file:
```sh
CLIPBOARD_LOCATION="~/.cache/clipboard"

clipboard_in() {
	echo $1 > $CLIPBOARD_LOCATION
}

clipboard_out() {
	echo $CLIPBOARD_LOCATION
}
```

# Flashcards
Copy text to clipboard using `xclip`:: `echo "text" | xclip -in`
<!--SR:!2022-12-21,5,230-->
Paste text from clipboard using `xclip`:: `xclip -out`
<!--SR:!2023-01-02,13,250-->
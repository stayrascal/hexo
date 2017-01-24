---
title: Bash Keyboard Shortcuts
date: 2016-04-24 20:55:52
description: "Some Shortcuts about Bash Keyboard"
tags: "Shortcuts"
---

## Moving the cursor:
	Ctrl + a  Go to the beginning of the line (Home)
	Ctrl + e  Go to the end of the line (End)
	Ctrl + p  Previous command (Up arrow)
	Ctrl + n  Next command (Down arrow)
	alt  + f  Forward (right) one word
	Alt  + b  Back (left) one word
	Ctrl + b  Backward one character
	Ctrl + f  Forward one character
	Ctrl + xx Toggle between the start of line and current cursor position
	
## Editing:
	Ctrl + L  Clear the screen, similar to the clear command
	Alt  + Del  Delete the word before the cursor
	Alt  + d  Delete the world after the cursor
	Ctrl + d  Delete character under the cursor
	Ctrl + h  Delete character before the cursor (Backspace)
	
	Ctrl + w  Cut the word before the cursor to the clipboard
	Ctrl + k  Cut the line after the cusor to the clipboard
	Ctrl + u  Cut/delete the line before the before the cursor to the clipboard
	
	Alt  + t  Swap current word with previous
	Ctrl + t  Swap the last two characters before the cursor (typo)
	Esc  + t  Swap the last two words before the cursor
	
	Ctrl + y  Paste the last thing to be cut (yank)
	Alt  + u  upper capitalize every character from the cursor to the end of the current word
	Alt  + l  Lower the case of every character from the cursor to the end of the current word
	Alt  + c  Capitalize the character under the cursor and move to the end of the word
	Alt  + r  Cancle the changes and put back the line as it was in the history (revert)
	Ctrl + _ Undo
	
## History:
	Ctrl + r  Recall the last command including the specified characters searches the command history as you type.
	Ctrl + p  Previoush command in history
	Ctrl + n  Next command in history
	
	Ctrl + s  Go back to the next most recent command
	Ctrl + o  Execute the command found via Ctrl+r or Ctrl+s
	Ctrl + g  Escape from history searching mode

## Process control:
	Ctrl + C  Interrupt/Kill whatever you are running
	Ctrl + l  Clear the screen
	Ctrl + s  Stop output to the screen
	Ctrl + q  Allow output to the screen
	Ctrl + D  Send an EOF marker, unless disabled by an option, this will close the current hsell
	Ctrl + Z  Send the signal Sigtetp to the current task, which suspends it.

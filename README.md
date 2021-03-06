# neoman

Read manpages faster than superman!

![neoman in action](https://media.giphy.com/media/xT0BKrEeXPeKVMgb84/giphy.gif)

## Features
- Manpage autocompletion
- Open in a split/vsplit/tabe or current window
- Open from inside a neovim terminal!
- Jump to manpages in specific sections through the manpage links
- Aware of modern manpages, e.g. sections are not just 1-8 anymore

## Install
Any plugin manager should work fine.

```vim
Plug 'nhooyr/neoman.vim' "vim-plug
```

## Usage
### Command
The command is as follows:

```vim
Neoman[!] [{sect}] {page}[({sect})]
```

Several ways to use it, probably easier to explain with a few examples.

```vim
Neoman printf
Neoman 3 printf
Neoman printf(3)
```

Neoman without any arguments will use `<cword>` as the page.

See `g:neoman_current_window` under settings for an explanation of the bang.

## Mappings
`<c-]>` or `K` to jump to a manpage under the cursor.  
`<c-t>` to jump back.
'q' to quit

You can also set the following in your `init.vim`/`.vimrc` and use `K` to jump to manpages globally for the word under the cursor.

```vim
set keywordprg=:Neoman
```

### Splits
Want to split/vsplit/tabe? Pretty simple.

```vim
:vsplit | Neoman! 3 printf
```

You can very easily make that a custom command or mapping.

### Command line integration

#### Neovim
You will need [nvr](https://github.com/mhinz/neovim-remote) for the super cool neovim terminal integration.

Add the following functions to your `.zshrc`/`.bashrc`

```zsh
function _nman {
	if [[ "$@" == "" ]]; then
		print "What manual page do you want?"
		return
	fi
	/usr/bin/man "$@" > /dev/null 2>&1
	if [[ "$?" != "0" ]]; then
		print "No manual entry for $*"
		return
	fi
	if [[ -z $NVIM_LISTEN_ADDRESS ]]; then
		/usr/bin/env nvim -c $cmd
	else
		nvr --remote-send "<c-n>" -c $cmd
	fi
}
function nman {
	cmd="Neoman $*"
	_nman "$@"
}
function nman! {
	cmd="Neoman! $*"
	_nman "$@"
}
```

#### Vim
```zsh
function _nman {
	if [[ "$@" == "" ]]; then
		print "What manual page do you want?"
		return
	fi
	/usr/bin/man "$@" > /dev/null 2>&1
	if [[ "$?" != "0" ]]; then
		print "No manual entry for $*"
		return
	fi
	vim -c $cmd
}
function nman {
	cmd="Neoman $*"
	_nman "$@"
}
function nman! {
	cmd="Neoman! $*"
	_nman "$@"
}
```

#### Autocomplete
##### zsh
```zsh
compdef nman="man"
compdef nman!="man"
```

##### bash
```bash
complete -o default -o nospace -F _man nman
complete -o default -o nospace -F _man nman!
```

Use `nman`/`nman!` to open the manpages. `nman!` works the same way as `:Neovim!`.

I've really only tested this with zsh, if you have any problems with bash, please open a issue!

### Settings
`g:neoman_current_window`  
If set, open the manpage in the current window, else attempt to find the currently open neoman window and use that. You can also use the bang on `:Neoman` to alternate between the two behaviors.

By default, it is not set


## Contributing

I'm very open to new ideas, new features, anything really. Open up an issue, send me a PR/email.

TODO:
-----
- [ ] Vim docs
- [ ] More mappings

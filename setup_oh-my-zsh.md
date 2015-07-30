**install oh-my-zsh**

	curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
    
**setup theme**

	sudo vi ~/.zshrc
    
replace ZSH_THEME="robbyrussell" with ZSH_THEME="agnoster"
save and exit

https://github.com/robbyrussell/oh-my-zsh/issues/1906

**install vim-powerline patched fonts**

[vim-powerline patched fonts](https://gist.github.com/qrush/1595572 "vim-powerline patched fonts")

download and install vim-powerline patched fonts

**enable history-substring-search plugin**

Activate the plugin in ~/.zshrc:

	plugins=( [plugins...] history-substring-search [plugins...] )


**enable zsh-syntax-highlighting plugin**

[zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting#with-oh-my-zsh "zsh-syntax-highlighting")

follow the instructions

**setup itern2 Color Schemes**

[Iterm2-color-schemes](http://iterm2colorschemes.com/ "Iterm2-color-schemes")


**disable autocorrect in zsh**
	
    vi  ~/.zshrc

Uncomment following line if you want to disable command autocorrection

    DISABLE_CORRECTION="true"
    
**fix false font**

    cd ~/.oh-my-zsh/themes/
    git checkout  d6a36b1 agnoster.zsh-theme
    
[A few invalid symbols in 'agnoster.zsh-theme'](https://github.com/robbyrussell/oh-my-zsh/issues/1906 "A few invalid symbols in 'agnoster.zsh-theme'")


**reference**
[agnoster.zsh-theme](https://gist.github.com/agnoster/3712874 "agnoster.zsh-theme")

# Git

## ~/.gitconfig

```toml
[user]
        name = Mohammadjavad Hoseini
        email = mhossini3458@gmail.com
        username = vector-mj
[init]
        defaultBranch = master
[core]
        editor = vim
[color]
        ui=auto
[color "branch"]
        current = yellow bold
        local = green bold
        remote = cyan bold
[color "diff"]
        meta = yellow bold
        frag = magenta bold
        old = red bold
        new = green bold
        whitespace = red reverse
[color "status"]
        added = green bold
        changed = yellow bold
        untracked = red bold
[alias]
lg1 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)' --all
lg2 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(auto)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)'
lg = lg1
```


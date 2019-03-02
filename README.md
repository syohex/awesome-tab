<img src="./screenshot.png">


Table of Contents
=================

* [What is it?](#what-is-it)
     * [Installation](#installation)
     * [Usage.](#usage)
     * [Customize](#customize)


# What is it?

Emacs package to provide out-of-the-box configuration to use tabs.

### Installation

1. Clone this repository

```
git clone --depth=1 https://github.com/manateelazycat/awesome-tab.git
```

2. Move powerline-separators.el, powerline-themes.el, powerline.el, awesome-tab.el to your load-path.

The load-path is usually `~/elisp/`.

It's set in your `~/.emacs` or `~/.emacs.d/init.el` like this:

```Elisp
(add-to-list 'load-path (expand-file-name "~/elisp"))

(require 'awesome-tab)

(awesome-tab-mode t)
```

If you are using [Use-packge](https://github.com/jwiegley/use-package), the configuration will look like this

```ELisp
(use-package awesome-tab
  :load-path "path/to/your/awesome-tab.el"
  :config
  (awesome-tab-mode t)
)
```

3. Reload your emacs configuration using `M-x eval-buffer` or restarting emacs


### Usage.

| Command                                         | Description                                                                           |
| :--------                                       | :----                                                                                 |
| awesome-tab-switch-group                        | Switch awesome-tab group by ido fuzz match                                            |
| awesome-tab-select-beg-tab                      | Select first tab of current group                                                     |
| awesome-tab-select-end-tab                      | Select last tab of current group                                                      |
| awesome-tab-forward-tab-other-window            | Select next tab in other window                                                       |
| awesome-tab-backward-tab-other-window           | Select previous tab in other window                                                   |
| awesome-tab-backward-tab                        | Select the previous available tab                                                     |
| awesome-tab-forward-tab                         | Select the next available tab                                                         |
| awesome-tab-backward-group                      | Go to selected tab in the previous available group                                    |
| awesome-tab-forward-group                       | Go to selected tab in the next available group                                        |
| awesome-tab-backward                            | Select the previous available tab, depend on setting of ```awesome-tab-cycle-scope``` |
| awesome-tab-forward                             | Select the next available tab, depend on setting of ```awesome-tab-cycle-scope```     |
| awesome-tab-kill-other-buffers-in-current-group | Kill other buffers of current group                                                   |
| awesome-tab-kill-all-buffers-in-current-group   | Kill all buffers of current group                                                     |
| awesome-tab-kill-match-buffers-in-current-group | Kill buffers match extension of current group                                         |
| awesome-tab-keep-match-buffers-in-current-group | Keep buffers match extension of current group                                         |
| awesome-tab-move-current-tab-to-left            | Move current tab to left                                                              |
| awesome-tab-move-current-tab-to-right           | Move current tab to right                                                             |

If you're helm fans, you need add below code in your helm config:

```Elisp
(awesome-tab-build-helm-source)
```

Then add ```helm-source-awesome-tab-group``` in ```helm-source-list```

### Customize

##### Theme

| Option                  | Description                |
| :--------               | :----                      |
| awesome-tab-background-color | Background color of awesome-tab |
| awesome-tab-selected     | Active tab color           |
| awesome-tab-unselected   | Inactive tab color         |

###### Hide rules.
Awesome tab hide some tabs with regular expression that controller by function ```awesome-tab-hide-tab-function```

Default hide function is ```awesome-hide-tab```

```
(defun awesome-tab-hide-tab (x)
  (let ((name (format "%s" x)))
    (and
     (not (string-prefix-p "*epc" name))
     (not (string-prefix-p "*helm" name))
     (not (string-prefix-p "*Compile-Log*" name))
     (not (string-prefix-p "*lsp" name))
     (not (and (string-prefix-p "magit" name)
               (not (file-name-extension name))))
     )))
```

Tab will hide if ```awesome-tab-hide-tab-function``` return nil, you can write your own code to customize hide rules.

###### Group rules.
Awesome tab use ```awesome-tab-buffer-groups-function``` to control tab group.
Default group function is ```awesome-tab-buffer-groups```

```
(defun awesome-tab-buffer-groups ()
  "`awesome-tab-buffer-groups' control buffers' group rules.

Group awesome-tab with mode if buffer is derived from `eshell-mode' `emacs-lisp-mode' `dired-mode' `org-mode' `magit-mode'.
All buffer name start with * will group to \"Emacs\".
Other buffer group by `awesome-tab-get-group-name' with project name."
  (list
   (cond
    ((or (string-equal "*" (substring (buffer-name) 0 1))
         (memq major-mode '(magit-process-mode
                            magit-status-mode
                            magit-diff-mode
                            magit-log-mode
                            magit-file-mode
                            magit-blob-mode
                            magit-blame-mode
                            )))
     "Emacs")
    ((derived-mode-p 'eshell-mode)
     "EShell")
    ((derived-mode-p 'emacs-lisp-mode)
     "Elisp")
    ((derived-mode-p 'dired-mode)
     "Dired")
    ((memq major-mode '(org-mode org-agenda-mode diary-mode))
     "OrgMode")
    (t
     (awesome-tab-get-group-name (current-buffer))))))
```

This function is very simple switch logic, you can write your own code to group tabs.

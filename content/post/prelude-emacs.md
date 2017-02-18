+++
title = "Prelude Emacs"
date = "2015-12-05"
comments = true
tags = ["emacs"]
+++

# echo "emacs --daemon" >> ~/.xstartup

```.shell
figlet "Emacs + Org-mode = Awesome"
```

# Reference Card: Emacs 101
- I always start by #learning how to learn#.
- I always had souls around me who respectfully insisted #[RTFM](https://en.wikipedia.org/wiki/RTFM)#!!!
- Emacs got a nice way for this. =C-h k <key>= will pop a
  documentation of corresponding key.
- To know about =C-n=, I would simply do a =C-h k C-n=.

## Motion

```.pre
| entity to move over                     | backward | forward |
|-----------------------------------------+----------+---------|
| character                               | C-b      | C-f     |
| word                                    | M-b      | M-f     |
| line                                    | C-p      | C-n     |
| go to line beginning (or end)           | C-a      | C-e     |
| sexp                                    | C-M-b    | C-M-f   |
| go to buffer beginning (or end)         | M-<      | M->     |
| scroll to next screen                   | C-v      |         |
| scroll to previous screen               | M-v      |         |
| scroll left                             | C-x <    |         |
| scroll right                            | C-x >    |         |
| scroll current line to center of screen | C-u C-l  |         |
|-----------------------------------------+----------+---------|
```

## Misc

```.pre
| Shortcut     | Description                                |
|--------------+--------------------------------------------|
| C-w          | Cut                                        |
| M-w          | Copy                                       |
| C-y          | Paste                                      |
| C-x 1        | would kill all windows but the active one. |
| M-!          | execute a shell command                    |
| M-:          | eval emacs-lisp                            |
| C-u 70 C-x f | sets column width to 70                    |
|--------------+--------------------------------------------|
```

# ctrl <-> capslock
- Had seen many using their palm edge to hit ctrl which might sound
  easy with an external keyboard but definitely not with the one that
  comes built-in with laptop.
- So I choose to swap left ctrl key with capslock for ergonomic
  reasons.

```.shell
    # .xinitrc
    setxkbmap -option ctrl:swapcaps
```
# Add package repositories

```.elisp
;; -#- lexical-binding: t; mode: emacs-lisp -#-
(require 'package)
(custom-set-variables
 '(package-archives
   (quote
    (("org" . "http://orgmode.org/elpa/")
     ("melpa" . "http://melpa.milkbox.net/packages/")
     ("melpa-stable" . "http://stable.melpa.org/packages/")
     ("marmalade" . "http://marmalade-repo.org/packages/")
     ("gnu" . "http://elpa.gnu.org/packages/")))))

(package-initialize)
```

# Use use-package

```.elisp
;; Bootstrap `use-package'
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))

(eval-when-compile
  (require 'use-package))
```

# Theme

```.elisp
;; start your day with todo.org
;  (setq initial-buffer-choice "~/org/non-public/org/todo.org")
(setq inhibit-startup-screen t)

  ;; user details for html exports / mails / etc
  (setq user-full-name "Shanthakumar")
  (setq user-mail-address "mail@shanth.tk")
  (setq org-export-with-email t)

  ;; I don't have lot of screen space for menus
  (menu-bar-mode -1)
  (scroll-bar-mode -1)
  (tool-bar-mode -1)

  ; Don't wander the cursor
  (scroll-lock-mode t)


  ;; 175% baterry full ??
  (display-battery-mode 1)
  (setq battery-mode-line-format "%p")

  ;; hey where is my clock ?
  (setq display-time-format " %H:%M %b %d %a ")
  (display-time-mode 1)

  ;; oh my eyes!!
  (use-package solarized                
    :ensure solarized-theme
  ;  :disabled t
    :config
    (load-theme 'solarized-dark 'no-confirm))

  (use-package zenburn                   
    :ensure zenburn-theme
    :disabled t
    :defer t
    :init (load-theme 'zenburn 'no-confirm))

  (set-face-attribute 'default nil :font "inconsolata" :height 170 )
  (set-frame-font  "inconsolata" nil t)

; make mode-line look ❤ ️cool ❤️
  (use-package powerline
    :ensure t
    :config (powerline-center-theme))

; compact mode-line
(use-package smart-mode-line
  :ensure t
  :defer t)

  ; Highlight the current line
  (use-package hl-line                    
    :init (global-hl-line-mode 1))

  ; match parens
  (show-paren-mode t)

  ; Highlight delimiters by depth
  (use-package rainbow-delimiters         
    :ensure t
    :defer t
    :init (dolist (hook '(text-mode-hook prog-mode-hook))
            (add-hook hook #'rainbow-delimiters-mode)))

  ;; classy green
  (set-cursor-color "#00ff00")

  ;; pretty gracile
  (setq default-cursor-type '(hbar . 1))

  ;; Stop winking!
  (blink-cursor-mode 0)
```

# Behaviour

```.elisp
   ;; beep beep... just shut the eff up!
  (setq visible-bell 1)

  ;; use space for tabs
  (setq-default indent-tabs-mode nil)

  ;; automatically wrap text to fill-column size
  (auto-fill-mode 1)

  ;; set column width for org ascii export
  (setq org-ascii-text-width 60)

  ; single stroke please ♉
  (fset 'yes-or-no-p 'y-or-n-p)

  ;; Enable focus‼️ I ain't a newbie anymore :P
  (put 'narrow-to-region 'disabled nil)

  ; Fontify number literals
  (use-package highlight-numbers          
    :ensure t
    :defer t
    :init (add-hook 'prog-mode-hook #'highlight-numbers-mode))

  ; Fontify color values in code
  (use-package rainbow-mode               
    :ensure t
    :bind (("C-c t r" . rainbow-mode))
    :config (add-hook 'css-mode-hook #'rainbow-mode))

  ; Highlight sexps
  (use-package highlight-sexp
    :ensure t
    :diminish ""
    :init (setq hl-sexp-background-color "#00171d")
    :config (dolist (hook '(clojure-mode-hook
                             lisp-mode-hook
                             emacs-lisp-mode-hook
                             clojurescript-mode-hook))
                     (add-hook hook #'highlight-sexp-mode)))

; Helm frontend for Flycheck errors
(use-package helm-flycheck              
  :ensure t
  :bind (("C-c e h" . helm-flycheck)))

; Insert non-breaking spaces on the fly
(use-package tildify                    
  :disabled t
  :bind (("C-c x t" . tildify-region))
  :init (dolist (hook '(markdown-mode-hook
                        latex-mode-hook
                        rst-mode-hook))
          (add-hook hook #'tildify-mode))
  ;; Use the right space for LaTeX
  :config (add-hook 'latex-mode-hook
                    (lambda () (setq-local tildify-space-string "~"))))

;; No-nonsense way of selection ☕
(use-package expand-region
  :ensure expand-region
  :defer t
  :bind ("C-=" . er/expand-region))

(use-package multi-term
  :ensure t
  :defer t
  :bind (("C-x t" . multi-term))
  :init
  (setq multi-term-program-switches "--login")
  (add-hook 'term-mode-hook (lambda()
                              (yas-minor-mode -1))))
```

# Autocomplete

```.elisp
 ;; autocomplete-fu
(require `ido)
(setq ido-enable-flex-matching t)
(setq ido-everywhere t)
(ido-mode 1)

(use-package yasnippet                
  :ensure t
  :defer t
  :init (yas-global-mode)
  :diminish (yas-minor-mode . " Ⓨ"))

;; start emacs with a terminal
(multi-term)

(use-package company                  
  :ensure t
  :init (global-company-mode)
  :config (setq company-tooltip-align-annotations t
                company-tooltip-flip-when-above t
                ;; Easy navigation to candidates with M-<n>
                company-show-numbers t)
  :diminish company-mode)

; Show help in tooltip
(use-package company-quickhelp        
  :ensure t
  :defer t
  :init (with-eval-after-load 'company
          (company-quickhelp-mode)))

; Sort company candidates by statistics
(use-package company-statistics    
  :ensure t
  :defer t
  :init (with-eval-after-load 'company
          (company-statistics-mode)))

; Completion for Math symbols
(use-package company-math            
  :ensure t
  :defer t
  :init (with-eval-after-load 'company
          ;; Add backends for math characters
          (add-to-list 'company-backends 'company-math-symbols-unicode)
          (add-to-list 'company-backends 'company-math-symbols-latex)))

; Emojis completion like Github/Slack
(use-package company-emoji           
  :ensure t
  :defer t
  :init (with-eval-after-load 'company
          (add-to-list 'company-backends 'company-emoji)))

; Helm frontend for company
(use-package helm-company               
  :ensure t
  :defer t
  :init (with-eval-after-load 'company
          ;; Use Company for completion
          (bind-key [remap completion-at-point] #'helm-company company-mode-map)
          (bind-key "C-:" #'helm-company company-mode-map)
          (bind-key "C-:" #'helm-company company-active-map)))
```

# Projects

```.elisp
 (use-package projectile
  :ensure t
  :defer t
  :diminish projectile-mode
  :config
  (progn
    (setq projectile-keymap-prefix (kbd "C-c p"))
    (setq projectile-completion-system 'default)
    (setq projectile-enable-caching t)
    (setq projectile-indexing-method 'alien)
    (add-to-list 'projectile-globally-ignored-files "node-modules"))
  :config
  (projectile-global-mode))

(use-package helm-projectile
   :defer t :ensure t
   :ensure helm-projectile)
```

# IRC
From : [Source](http://pages.sachachua.com/.emacs.d/Sacha.html#orgheadline157).

```.elisp
 (use-package erc
  :ensure t :defer t
  :config
  (setq erc-hide-list '("PART" "QUIT" "JOIN"))
  (setq erc-autojoin-channels-alist '(("freenode.net"
        "#org-mode"
        "#emacs"
         "#emacs-beginners"))
 erc-server "irc.freenode.net"
 erc-nick "shanthakumar")
  (defun erc-cmd-OPME ()
    "Request chanserv to op me."
    (erc-message "PRIVMSG"
                 (format "chanserv op %s %s"
                         (erc-default-target)
                         (erc-current-nick)) nil))

  (defun erc-cmd-DEOPME ()
    "Deop myself from current channel."
    (erc-cmd-DEOP (format "%s" (erc-current-nick))))
  )
```

# Spelling and syntax checking

```.elisp
 (use-package ispell                    
  :defer t
  :config
  (progn
    (setq ispell-program-name  (executable-find "aspell")

          ispell-dictionary "en_US"     ; Default dictionnary
          ispell-silently-savep t       ; Don't ask when saving the private dict
          ;; Increase the height of the choices window to take our header line
          ;; into account.
          ispell-choices-win-default-height 5)

    (unless ispell-program-name
      (warn "No spell checker available.  Install Hunspell or ASpell."))))

(use-package flyspell                   ; On-the-fly spell checking
  :bind (("C-c t s" . flyspell-mode))
  :init (progn (dolist (hook '(text-mode-hook message-mode-hook))
                 (add-hook hook 'turn-on-flyspell))
               (add-hook 'prog-mode-hook 'flyspell-prog-mode))
  :config
  (progn
    (setq flyspell-use-meta-tab nil
          ;; Make Flyspell less chatty
          flyspell-issue-welcome-flag nil
          flyspell-issue-message-flag nil)

    ;; Free C-M-i for completion
    (define-key flyspell-mode-map "\M-\t" nil)
    ;; Undefine mouse buttons which get in the way
    (define-key flyspell-mouse-map [down-mouse-2] nil)
    (define-key flyspell-mouse-map [mouse-2] nil))
  :diminish (flyspell-mode . " ⓢ"))

(use-package flycheck                   ; On-the-fly syntax checking
  :ensure t
  :bind (("C-c e l" . list-flycheck-errors)
         ("C-c e n" . flycheck-next-error)
         ("C-c e p" . flycheck-previous-error)
         ("C-c e c" . flycheck-buffer)
         ("C-c e C" . flycheck-clear)
         ("C-c e f" . flycheck-first-error)
         ("C-c e w" . flycheck-copy-errors-as-kill)
         ("C-c t f" . flycheck-mode))
  :init (global-flycheck-mode)
  :config (progn
            (setq flycheck-standard-error-navigation nil
                  flycheck-display-errors-function
                  #'flycheck-display-error-messages-unless-error-list
                  flycheck-scalastylerc "scalastyle_config.xml")

            ;; Use italic face for checker name
            (set-face-attribute 'flycheck-error-list-checker-name nil
                                :inherit 'italic))
  :diminish (flycheck-mode . " Ⓢ"))
```

# Batman Jump to positions

```.elisp
 (use-package avy :ensure t)
(use-package avy-zap
  :ensure t
  :bind
  (("M-z" . avy-zap-up-to-char-dwim)
   ("M-Z" . avy-zap-to-char-dwim)))
```

# Search

```.elisp
 (use-package engine-mode
  :ensure t
  :config
  (progn
    (defengine my-blog "https://www.google.ca/search?q=site:shanth.tk+%s" :keybinding "b")
    (defengine stack-overflow "http://stackoverflow.com/search?q=%s" :keybinding "s")
    (defengine mail "https://mail.google.com/mail/u/0/#search/%s" :keybinding "m")
    (defengine google "http://google.com/search?q=%s" :keybinding "g")
    (defengine google-eww "http://google.com/search?q=%s" :keybinding "/" :browser 'eww-browse-url)
    (defengine emacswiki "http://google.com/search?q=site:emacswiki.org+%s" :keybinding "e")
    (bind-key# "C-c /" 'my/engine-mode-hydra/body)
    (engine-mode)))
```

# Key Bindings

```.elisp
 (use-package switch-window
  :ensure t
  :bind (("C-x o" . switch-window)))

(use-package ace-window
  :ensure t)

(use-package hydra :ensure t)
  (defhydra my/window-movement ()
    ("<left>" windmove-left)
    ("<right>" windmove-right)
    ("<down>" windmove-down)
    ("<up>" windmove-up)
    ("y" other-window "other")
    ("h" switch-window "switch-window")
    ("f" find-file "file")
    ("F" find-file-other-window "other file")
    ("v" (progn (split-window-right) (windmove-right)) "Vertical")
    ("o" delete-other-windows :color blue "delete others")
    ("a" ace-window)
    ("s" ace-swap-window "swap")
    ("d" delete-window "delete")
    ("D" ace-delete-window "ace delete")
    ("i" ace-maximize-window "maximize")
    ("b" helm-buffers-list "buffers")
    ("q" nil))
  (defhydra join-lines ()
    ("<up>" join-line)
    ("<down>" (join-line 1))
    ("t" join-line)
    ("n" (join-line 1)))
  (defhydra my/org (:color blue)
    "Convenient Org stuff."
    ("a" (org-agenda nil "a") "Agenda"))
  (defhydra my/key-chord-commands ()
    "Main"
    ("k" kill-sexp)
    ("h" my/org-jump :color blue)
    ("x" my/org-finish-previous-task-and-clock-in-new-one "Finish and clock in" :color blue)
    ("i" my/org-quick-clock-in-task "Clock in" :color blue)
    ("b" helm-buffers-list "buffers" :color blue)
    ("f" find-file :color blue)
    ("a" my/org-check-agenda :color blue)
    ("c" (call-interactively 'org-capture) "capture" :color blue)
    ("t" (org-capture nil "T") "Capture task")
    ("." repeat "repeat")
    ("C-t" transpose-chars)
    ("o" my/org-off-my-computer :color blue)
    ("w" my/engine-mode-hydra/body "web" :exit t)
    ("m" imenu "imenu" :color blue)
    ("l" org-insert-last-stored-link)
    ("L" my/org-insert-link)
    ("+" text-scale-increase)
    ("-" text-scale-decrease)
    ("s" sha/format-mail-header)
    ("r" sha/format-mail-interleave)
    ("z" undo)
    ("x" er/expand-region)
    ("<RET>" nil))

  (defhydra my/engine-mode-hydra (:color blue)
    "Engine mode"
    ("b" engine/search-my-blog "blog")
    ("s" engine/search-stack-overflow "stackoverflow")
    ("m" engine/search-mail "mail")
    ("g" engine/search-google "google")
    ("/" engine/search-google-eww "google-eww")    
    ("e" engine/search-emacswiki "emacswiki"))

(defun my/key-chord-define (keymap keys command)
  "Define in KEYMAP, a key-chord of two keys in KEYS starting a COMMAND.
\nKEYS can be a string or a vector of two elements. Currently only elements
that corresponds to ascii codes in the range 32 to 126 can be used.
\nCOMMAND can be an interactive function, a string, or nil.
If COMMAND is nil, the key-chord is removed.

MODIFICATION: Do not define the transposed key chord.
"
  (if (/= 2 (length keys))
      (error "Key-chord keys must have two elements"))
  ;; Exotic chars in a string are >255 but define-key wants 128..255 for those
  (let ((key1 (logand 255 (aref keys 0)))
        (key2 (logand 255 (aref keys 1))))
    (define-key keymap (vector 'key-chord key1 key2) command)))

(use-package key-chord
  :ensure t
  :init
  (progn
    (fset 'key-chord-define 'my/key-chord-define)
    (setq key-chord-one-key-delay 0.16)
    (key-chord-mode 1)
    ;; k can be bound too
    (key-chord-define-global "uu"     'undo)
    (key-chord-define-global "kk"     'my/org/body)
    (key-chord-define-global "ww"     'avy-goto-word-1)
    (key-chord-define-global "yy"    'my/window-movement/body)
    (key-chord-define-global "jw"     'switch-window)
    (key-chord-define-global "jl"     'avy-goto-line)
    (key-chord-define-global "j."     'join-lines/body)
    ;(key-chord-define-global "jZ"     'avy-zap-to-char)
    (key-chord-define-global "ff"     'find-file)
    (key-chord-define-global "hh"     'my/key-chord-commands/body)
    (key-chord-define-global "xx"     'er/expand-region)
    (key-chord-define-global "JJ"     'my/switch-to-previous-buffer)))

;; M-n or M-p to move to symbol under point.
(use-package smartscan
  :defer t
  :ensure t
  :config (global-smartscan-mode t))
```

# Setup gnus for Mails

```.elisp
 (custom-set-variables
 '(send-mail-function (quote smtpmail-send-it))
 '(smtpmail-smtp-server "mail.pawnmail.com")
 '(smtpmail-smtp-service 25))
```
Write your mail server config to ~/.authinfo (perhaps use authinfo.gpg)
#+BEGIN_SRC text :tangle ~/.authinfo
machine mail.pawnmail.com login mail@shanth.tk port imaps
machine mail.pawnmail.com login mail@shanth.tk port 993
```
## Make your emails look nicer ♥️
```.elisp (defun sha/format-mail-header ()
  (interactive)
  (with-current-buffer (current-buffer)
    (let ((beg (region-beginning))
          (end (region-end))
          (fill-column 60))
      (goto-char beg)
      (insert "Hi,\n\n")
      (forward-line 0)
      (insert "---")
      (end-of-line)
      (newline)
      (fill-individual-paragraphs (point) end)
      (goto-char (+ 5 beg))
      (forward-line)
      (while (<= (point) (+ 3 1 (count-lines beg end) end))
        (insert "| ")
        (forward-line))
      (insert "\\--\n\n"))))

(defun sha/format-mail-interleave ()
  (interactive)
  (with-current-buffer (current-buffer)
    (let ((beg (region-beginning))
          (end (region-end))
          (fill-column 60))
      (goto-char beg)
      (forward-line 0)
      (newline)
      (insert "---")
      (newline)
      (goto-char end)
      (end-of-line)
      (newline)
      (goto-char (+ beg 3 2))
      (fill-individual-paragraphs (point) end)
      (while (<= (point) (+ 1 3 1 (count-lines beg end) end))
        (insert "| ")
        (forward-line))
      (insert "\\--\n\n"))))
```

# Task Management

```.elisp
 (use-package org
  :ensure org-plus-contrib
  :mode (("\\.org$" . org-mode)
         ("\\.gpg$" . org-mode))
  :config (progn

;  encrypt files with my public key
    (setq epa-file-encrypt-to '("mail@shanth.tk"))

    (setq org-columns-default-format "%50ITEM(TASK) %11TODO %16DEADLINE %16SCHEDULED %TAGS")

  ; org-mode key-bindings
  (global-set-key "\C-cl" 'org-store-link)
  (global-set-key "\C-cc" 'org-capture)
  (global-set-key "\C-ca" 'org-agenda)
  (global-set-key "\C-cb" 'org-iswitchb)

  (setq org-log-done t)
  (setq org-indent-mode t)
  (setq org-startup-indented t)
  (setq org-startup-folded 'content)
  (setq org-indent-indentation-per-level 2)
  (setq org-directory "~/org")
  (setq org-export-allow-bind-keywords t)

  (setq org-todo-keywords
        '((sequence "TODO" "DEFERRED" "IN-PROGRESS" "DONE")))

  (setq org-todo-keyword-faces
        (quote (("DONE" :foreground "medium green" :weight bold)
                ("DEFERRED" :foreground "medium yellow" :weight bold)
                ("IN-PROGRESS" :foreground "medium blue" :weight bold)
                ("TODO" :foreground "red" :weight bold))))

  (setq org-agenda-files (quote ("~/org/non-public/org/")))))

```

# Diary, Status Updates, todo.org, Vocabulary and Scratch
- Create [custom capture](http://orgmode.org/manual/Template-elements.html#Template-elements) [templates] (https://www.gnu.org/software/emacs/manual/html_node/org/Template-expansion.html#Template-expansion) to log timely diary and status
  updates.
- Use =C-c c= or =C-c d= or =C-c t= to input desired logs.

```.elisp
   (setq org-capture-templates
      '(("t" "Todo" entry (file+headline "~/org/non-public/org/todo.org" "Tasks")
         "# TODO %?\n  %i\n  %a")
        ("d" "Diary Entry" entry (file+datetree+prompt "~/org/non-public/org/timelog.org")
         "## %U - %^{Activity}%(org-set-tags)\t:DIARY:\n%?")
        ("v" "Vocabulary" entry (file+headline "~/org/non-public/org/vocabulary.org" "Vocabulary")
         "# %^{Word}\n%?")
        ("s" "Scratch" entry (file+headline "~/org/non-public/org/scratch.org" "Scratch")
         "# %^{Heading}\n%?")
        ("b" "Bookmarks" entry (file+headline "~/org/non-public/org/bookmarks.org" "Bookmarks")
         "# %^{Topic}\n%?")
        (" " "Capture one-liner Status" entry (file+datetree "~/org/non-public/org/timelog.org")
         "## %U - %^{Activity}%(org-set-tags)" :immediate-finish t)
        ("u" "Capture one-liner Status with link" entry (file+datetree "~/org/non-public/org/timelog.org")
         "## %U - %^{Activity} %^L%(org-set-tags)" :immediate-finish t)
        ("c" "Capture Log" entry (file+datetree "~/org/non-public/org/timelog.org")
         "## %U - %^{Activity}%(org-set-tags)\n%?")))
```
- Add tags to =~/org/non-public/org/timelog.org=

```.org
#+TITLE: Timelog
#+SETUPFILE: ~/.emacs.d/org-templates/blog.org
#+TAGS: { done(') now(,) { consider(.) plan(;) try(t) } went(w) { college(g) meetup(m) { chennairb(r) ilugc(i) } }  } { use(u) change(n) } wanna(a) { read(/) write(-) create(c) play(p) } pest(e) find(f) { how(h) why(y) } { abort(u) setup(s) } fix(x) { think(k) realize(v) opinion(o) } fail(l) miff(z)
```
- Initialize a git repo at =~/org/non-public/org= and push it to
  bitbucket or anything that is private.
- Create symlink to html export of ~/org/non-public/ to make it
  accessible locally at =http://localhost/non-public/=

```.shell
ln -s ~/org/non-public/ ~/org/blog/
```

# Thesaurus

```.elisp
(use-package thesaurus
  :ensure t
  :defer t
  :config
  ;; get the API key from https://words.bighugelabs.com
  (thesaurus-set-bhl-api-key-from-file "~/bhl.api"))
```

# Screen-casting

```.elisp
(defun sha/screencast (&optional output-file)
  (let ((output-file
         (or output-file
             (concat "/tmp/"
                     (format "%S" (abs (random)))
                     ".mp4"))))
    (message  "screen-cast started")
    (start-process "screencasting"
                   (get-buffer-create "#screencast-buffer#")
                   "ffcast"
                   "rec"
                   output-file)
    output-file))

(defun sha/screencast-handler ()
  (interactive)
  (if (process-status "screencasting")
      (progn
        (process-send-string "screencasting" "q")
        (message "screen-cast stopped"))
    (sha/screencast)))

(global-set-key (kbd "s--") 'sha/screencast-handler )

(defun sha/gif-encode (input-file &optional scale frames)
  (setq sha/gif/frames (or frames "15"))
        (setq sha/gif/scale (or scale "720"))
        (setq sha/gif/palette "/tmp/palette.png")
        (setq sha/gif/filters
         (concat "fps=" sha/gif/frames
                 ",scale=" sha/gif/scale
                 ":-1:flags=lanczos"))
    (set-process-sentinel
     (start-process "gifcasting"
                    (get-buffer-create "#gifcast-buffer#")
                    "ffmpeg" "-i" input-file
                    "-vf" (concat sha/gif/filters ",palettegen")
                    "-y" sha/gif/palette)
     (lambda (process event)
       (when (= 0 (process-exit-status process))
         (set-process-sentinel
          (start-process "gifcasting"
                         (get-buffer-create "#gifcast-buffer#")
                         "ffmpeg" "-i" input-file
                         "-i" sha/gif/palette
                         "-lavfi" (concat sha/gif/filters
                                          "[x]; [x][1:v] paletteuse")
                         "-y" (concat input-file ".gif"))
          (lambda (process event)
            (when (= 0 (process-exit-status process))
              (message "gif-casting done"))))))))

(defun sha/screencast-to-gif (&optional scale frames)
  (interactive)
  (sha/gif-encode (read-file-name "Enter .mp4 path : " "/tmp/")
                  scale frames)
  (message "Gif-casting started" ))
```

# Presentations

```.elisp
; beamer for LaTeX based presentations.
(require 'ox-beamer)
(use-package ox-reveal :ensure t)
; download reveal.js https://github.com/hakimel/reveal.js/
(setq org-reveal-root "/files/reveal.js")
```
- Create dirs to access presentations at http://localhost/presentations/

```.shell
# one for public
mkdir -p ~/org/blog/presentations/org

# one for private
mkdir -p ~/org/non-public/presentations/org
```

# Use Stack Exchange

```.elisp
(use-package sx                         ; StackExchange client for Emacs
  :ensure t
  :bind (("C-x a s a" . sx-ask)
         ("C-x a s s" . sx-tab-all-questions)
         ("C-x a s q" . sx-tab-all-questions)
         ("C-x a s f" . sx-tab-all-questions)
         ("C-x a s n" . sx-tab-newest)))

(use-package sx-compose                 ; Write questions/answers for Stack Exchange
  :ensure sx
  :defer t
  :config
  (progn
    ;; Don't fill in SX questions/answers, and use visual lines instead.  Plays
    ;; more nicely with the website.
    (add-hook 'sx-compose-mode-hook #'turn-off-auto-fill)
    (add-hook 'sx-compose-mode-hook #'visual-line-mode)

    ;; Clean up whitespace before sending questions
    (add-hook 'sx-compose-before-send-hook
              (lambda ()
                (whitespace-cleanup)
                t))

    (bind-key "M-q" #'ignore sx-compose-mode-map)))

(use-package sx-question-mode           ; Show Stack
  :ensure sx
  :defer t
  ;; Display questions in the same window
  :config (setq sx-question-mode-display-buffer-function #'switch-to-buffer))
```

# Use Twitter

```.elisp
  (require 'twittering-mode)
  (setq twittering-use-master-password t)
  (setq twittering-cert-file "/etc/ssl/certs/ca-certificates.crt")
```

# org-mode Code blocks

```.elisp   
;; enable source code blocks in org files
 (org-babel-do-load-languages
   'org-babel-load-languages
     (quote ((emacs-lisp . t)
             (dot . t)
             (ditaa . t)
             (ruby . t)
             (clojure . t)
             (haskell . t)
             (sh . t)
             (org . t)
             (plantuml . t)
             (latex . t))))

    ;; source code blocks for ruby haskell dot
    (add-to-list 'org-structure-template-alist
                 '("r" "#+BEGIN_SRC ruby :results ?value\n```"))
    (add-to-list 'org-structure-template-alist
                 '("d" "#+BEGIN_SRC dot :file ?tmp.svg\n```"))
    (add-to-list 'org-structure-template-alist
                 '("h" "#+BEGIN_SRC haskell :results ?value\n```"))
    (add-to-list 'org-structure-template-alist
                 '("s" "#+BEGIN_SRC sh :results output :exports both\n?```"))

    ; Do not prompt to confirm evaluation - just make sure what you run
    ; with sh src blocks - Don't try to demonstrate rm -rf / or a fork
    ; bomb :(){ :|:& };:
    (setq org-confirm-babel-evaluate nil)

    ;; display images inline
    (add-hook 'org-babel-after-execute-hook 'org-display-inline-images)

    ;; Export headline body as nodes
    (require 'ox-freemind)
    (custom-set-variables
    '(org-freemind-section-format (quote node)))
```
# Language Tooling
- [Source](https://github.com/lunaryorn/.emacs.d/blob/master/init.el).

```.elisp   
;; clojure cider repl as root
  (setenv "LEIN_ROOT" "t")
  (setq org-babel-clojure-backend 'cider)

(use-package clojure-mode
  :ensure t
  :mode (("\\.clj\\'" . clojure-mode)
         ("\\.edn\\'" . clojure-mode))
  :init
  (add-hook 'clojure-mode-hook #'yas-minor-mode)         
  (add-hook 'clojure-mode-hook #'linum-mode)             
  (add-hook 'clojure-mode-hook #'subword-mode)           
  (add-hook 'clojure-mode-hook #'smartparens-mode)       
  (add-hook 'clojure-mode-hook #'rainbow-delimiters-mode)
  (add-hook 'clojure-mode-hook #'eldoc-mode))
;  (add-hook 'clojure-mode-hook #'idle-highlight-mode))

(use-package cider
  :ensure t
  :defer t
  :init (add-hook 'cider-mode-hook #'clj-refactor-mode)
  :diminish subword-mode
  :config
  (setq nrepl-log-messages t                  
        cider-repl-display-in-current-window t
        cider-repl-use-clojure-font-lock t    
        cider-prompt-save-file-on-load 'always-save
        cider-font-lock-dynamically '(macro core function var)
        nrepl-hide-special-buffers t            
        cider-overlays-use-font-lock t)         
  (cider-repl-toggle-pretty-printing))

(use-package cider-eval-sexp-fu
  :defer t)

(use-package clj-refactor
  :defer t
  :ensure t
  :diminish clj-refactor-mode
  :config (cljr-add-keybindings-with-prefix "C-c C-m"))

(use-package smartparens
  :defer t
  :ensure t
  :diminish smartparens-mode
  :init
  (setq sp-override-key-bindings
        '(("C-<right>" . nil)
          ("C-<left>" . nil)
          ("C-)" . sp-forward-slurp-sexp)
          ("M-<backspace>" . nil)
          ("C-(" . sp-forward-barf-sexp)))
  :config
  (use-package smartparens-config)
  (sp-use-smartparens-bindings)
  (sp--update-override-key-bindings)
  :commands (smartparens-mode show-smartparens-mode))

; Graphviz
(use-package graphviz-dot-mode  
  :ensure t
  :defer t
  :config
  (setq graphviz-dot-indent-width 4))

(use-package alchemist
  :ensure t
  :init (setq alchemist-key-command-prefix (kbd "C-c ,"))
  :config (progn
            (require 'alchemist)
            (setenv "LC_ALL" "en_US.UTF-8")))

;;; Ruby REPL
(use-package inf-ruby                   
  :ensure t
  :defer t
  :init (add-hook 'ruby-mode-hook #'inf-ruby-minor-mode)
  :config
  ;; Easily switch to Inf Ruby from compilation modes to Inf Ruby
  (inf-ruby-switch-setup))

;;; Haskell

;; This Haskell setup needs:
;;
;; stack install hasktags haskell-docs hoogle hindent
;;
;; Additionally, to be installed from source:
;;
;; - https://github.com/chrisdone/ghci-ng

(use-package haskell-mode               ; Haskell editing
  :ensure t
  :defer t
  :config
  (progn
    (add-hook 'haskell-mode-hook #'subword-mode)           ; Subword navigation
    (add-hook 'haskell-mode-hook #'haskell-decl-scan-mode) ; Scan and navigate
                                        ; declarations
    ;; Insert module templates into new buffers
    (add-hook 'haskell-mode-hook #'haskell-auto-insert-module-template)

    ;; Automatically run hasktags
    (setq haskell-tags-on-save t
          haskell-process-type 'stack-ghci ; Use stack for interaction
          ;; Suggest adding/removing imports as by GHC warnings and Hoggle/GHCI
          ;; loaded modules respectively
          haskell-process-suggest-remove-import-lines t
          haskell-process-auto-import-loaded-modules t
          haskell-process-use-presentation-mode t ; Don't clutter the echo area
          haskell-process-show-debug-tips nil     ; Disable tips
          haskell-process-log t                   ; Log debugging information
          haskell-process-suggest-hoogle-imports t)

;    (when-let (ghci-ng (executable-find "ghci-ng"))
;      ;; Use GHCI NG from https://github.com/chrisdone/ghci-ng
;      (setq haskell-process-path-ghci ghci-ng)
;      (add-to-list 'haskell-process-args-cabal-repl
;                   (concat "--with-ghc=" ghci-ng)))

    (bind-key "C-c m d" #'haskell-describe haskell-mode-map)
    (bind-key "C-c m i" #'haskell-navigate-imports haskell-mode-map)
    (bind-key "C-c m f" #'haskell-cabal-visit-file haskell-mode-map)))

(use-package haskell                    ; Haskell tools
  :ensure haskell-mode
  :defer t
  :config
  (progn
    (bind-key "C-c m t" #'haskell-mode-show-type-at
              interactive-haskell-mode-map)
    (bind-key "C-c m j" #'haskell-mode-goto-loc
              interactive-haskell-mode-map)
    (bind-key "C-c m r" #'haskell-mode-find-uses
              interactive-haskell-mode-map)))

(use-package haskell-interactive-mode   ; Haskell REPL interaction
  :ensure haskell-mode
  :disabled t
  :defer t
  :config (add-hook 'haskell-interactive-mode-hook #'subword-mode))

(use-package stack-mode                 ; Stack IDE
  :ensure t
  :defer t
  :config (add-hook 'haskell-mode-hook #'stack-mode))

(use-package hyai                       ; Haskell Indentation as per John Tibell
  :ensure t
  :defer t
  :init (add-hook 'haskell-mode-hook #'hyai-mode)
  :diminish (hyai-mode . " ⓘ"))

(use-package hindent                    ; Automated Haskell indentation
  :ensure t
  :defer t
  :init (add-hook 'haskell-mode-hook #'hindent-mode)
  :diminish (hindent-mode . " Ⓘ"))

(use-package flycheck-haskell           ; Setup Flycheck from Cabal projects
  :ensure t
  :defer t
  :init (add-hook 'flycheck-mode-hook #'flycheck-haskell-setup))

(use-package helm-hoogle                ; Helm frontend for Hoogle
  :ensure t
  :defer t
  :init (with-eval-after-load 'haskell-mode
          (bind-key "C-c m h" #'helm-hoogle haskell-mode-map)))

;;; Web languages
(use-package web-mode                   ; Template editing
  :ensure t
  :defer t
  :mode "/templates?/.#\\.\\(php\\|html\\)\\'"
  :config
  (setq web-mode-markup-indent-offset 2))

(use-package js2-mode                   ; Javascript editing
  :ensure t
  :mode "\\.js\\'"
  :config (progn (setq-default js2-basic-offset 2)
                 (setq js2-global-externs '("angular"))

                 (add-hook 'js2-mode-hook
                           #'js2-highlight-unused-variables-mode)))

(use-package css-mode                   ; CSS editing
  :defer t
  :config
  (progn
    ;; Run Prog Mode hooks, because for whatever reason CSS Mode derives from
    ;; `fundamental-mode'.
    (add-hook 'css-mode-hook (lambda () (run-hooks 'prog-mode-hook)))

    ;; Mark css-indent-offset as safe local variable.
    (put 'css-indent-offset 'safe-local-variable #'integerp)))

(use-package css-eldoc                  ; Basic Eldoc for CSS
  :ensure t
  :commands (turn-on-css-eldoc)
  :init (add-hook 'css-mode-hook #'turn-on-css-eldoc))

;;; Misc programming languages
(use-package sh-script                  ; Shell scripts
  :mode ("\\.zsh\\'" . sh-mode)
  :config
  ;; Use two spaces in shell scripts.
  (setq sh-indentation 2                ; The basic indentation
        sh-basic-offset 2               ; The offset for nested indentation
        ))

(use-package nxml-mode                  ; XML editing
  :defer t
  ;; Complete closing tags, and insert XML declarations into empty files
  :config (setq nxml-slash-auto-complete-flag t
                nxml-auto-insert-xml-declaration-flag t))

;;; Databases
(use-package sql                        ; SQL editing and REPL
  :bind (("C-x a q" . sql-connect)))
```

# Version control

```.elisp
(use-package vc-hooks                   ; Simple version control
  :defer t
  :config
  ;; Always follow symlinks to files in VCS repos
  (setq vc-follow-symlinks t))

(use-package what-the-commit            ; Insert random commit messages
  :ensure t
  :bind (("C-c i w" . what-the-commit-insert)
         ("C-c v w" . what-the-commit)))

(use-package diff-hl                    ; Highlight hunks in fringe
  :ensure t
  :defer t
  :init (progn
          ;; Highlight changes to the current file in the fringe
          (global-diff-hl-mode)
          ;; Highlight changed files in the fringe of Dired
          (add-hook 'dired-mode-hook 'diff-hl-dired-mode)

          ;; Fall back to the display margin, if the fringe is unavailable
          (unless (display-graphic-p)
            (diff-hl-margin-mode))))

(use-package magit                      ; The one and only Git frontend
  :ensure t
  :bind (("C-c v c" . magit-clone)
         ("C-c v v" . magit-status)
         ("C-c v g" . magit-blame)
         ("C-c v l" . magit-log-buffer-file)
         ("C-c v p" . magit-pull))
  ;; Aggressively commit to WIP refs on any change
  :init (progn (magit-wip-after-save-mode)
               (magit-wip-after-apply-mode)
               (magit-wip-before-change-mode))
  :config (progn
            ;; Shut up, Magit
            (setq magit-revert-buffers 'silent
                  magit-save-repository-buffers 'dontask
                  magit-push-always-verify nil
                  magit-refs-show-commit-count 'all
                  ;; This is creepy, Magit
                  magit-revision-show-gravatars nil
                  ;; For some reason this doesn't work :(
                  ;; magit-completing-read-function
                  ;; #'helm-completing-read-with-cands-in-buffer
                  )

            ;; Set Magit's repo dirs for `magit-status' from Projectile's known
            ;; projects.  Initialize the `magit-repository-directories'
            ;; immediately after Projectile was loaded, and update it every time
            ;; we switched projects, because the new project might have been
            ;; unknown before
            (defun magit-set-repo-dirs-from-projectile ()
              "Set `magit-repo-dirs' from known Projectile projects."
              (let ((project-dirs (bound-and-true-p projectile-known-projects)))
                ;; Remove trailing slashes from project directories, because
                ;; Magit adds trailing slashes again, which breaks the
                ;; presentation in the Magit prompt.
                (setq magit-repository-directories
                      (mapcar #'directory-file-name project-dirs))))

            (with-eval-after-load 'projectile
              (magit-set-repo-dirs-from-projectile))

            (add-hook 'projectile-switch-project-hook
                      #'magit-set-repo-dirs-from-projectile))
  :diminish (magit-wip-after-save-local-mode
             magit-wip-before-change-mode))

(use-package git-commit                 ; Git commit message mode
  :ensure t
  :defer t
  :config
  ;; Oh, really?  Come on… I know what I'm doing…
  (remove-hook 'git-commit-finish-query-functions
               #'git-commit-check-style-conventions))

(use-package gitconfig-mode             ; Git configuration mode
  :ensure t
  :defer t)

(use-package gitignore-mode             ; .gitignore mode
  :ensure t
  :defer t)

(use-package gitattributes-mode         ; Git attributes mode
  :ensure t
  :defer t)

(use-package git-timemachine            ; Go back in Git time
  :ensure t
  :bind (("C-c v t" . git-timemachine)))

;; show commit messages for a change in popup.
(use-package git-messenger
  :ensure t :defer t
  :bind (("C-x v m" . git-messenger:popup-message)))

;;; Github integration
(use-package gh                         ; Github API library
  ;; Don't ensure it, since it's only brought in as dependency
  :ensure nil
  :defer t
  ;; Change the default profile.  The profile itself is set up via customize,
  ;; and includes auth data, to prevent it from storing tokens in Git config
  :config (setq gh-profile-default-profile "5hanth"))

(use-package magit-gh-pulls             ; Show Github PRs in Magit
  :ensure t
  :defer t
  :init (add-hook 'magit-mode-hook #'turn-on-magit-gh-pulls))

(use-package helm-open-github           ; Open Github pages for current repo
  :ensure t
  :bind (("C-c v G i" . helm-open-github-from-issues)
         ("C-c v G p" . helm-open-github-from-pull-requests)))

```

# Tangle prelude-emacs.org file to ~/.emacs
- This blog post is written in [literate programming style](https://en.wikipedia.org/wiki/Literate_programming).
- Files ~/.emacs, org-style.css, options templates and any other code
  used in this post can be [tangled](http://orgmode.org/manual/Extracting-source-code.html) to corresponding files in org-mode.
- Org file for this blog post is available in [my github pages repo](https://raw.githubusercontent.com/5hanth/5hanth.github.io/master/org/prelude-emacs.org).

```.elisp
;; your every .emacs code goes here like this in src blocks
;; give filename to extract code blocks with :tangle option in src block header
;; you can use C-c C-v t to tangle this org file and create ~/.emacs
```

```.elisp
  ;; C-c C-c on this block will generate ~/.emacs
    (org-babel-tangle) ; or C-c C-v t
```
- I simply edit this blog post and do =C-c C-v t= to make changes to my ~/.emacs file.

# It Will Evolve Over Years.

```.shell
figlet "Trust Me : )"
```

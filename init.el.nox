;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;                              MiniMacs - Minimal Emacs                          ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;


;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;                               ENCONDING SETTINGS                               ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(setq locale-coding-system 'utf-8)	        ; utf-8 setting
(set-terminal-coding-system 'utf-8)		; utf-8 setting
(set-keyboard-coding-system 'utf-8)		; utf-8 setting
(set-selection-coding-system 'utf-8)		; utf-8 setting
(prefer-coding-system 'utf-8)		        ; utf-8 setting

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;                                STARTUP SETTINGS                                ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(setq-default
 inhibit-startup-screen t                         ; Disable start-up screen
 inhibit-startup-message t                        ; Disable startup message
 inhibit-startup-echo-area-message t              ; Disable initial echo message
 initial-scratch-message "")                      ; Empty the initial *scratch* buffer

(menu-bar-mode -1)   			        ; Disable menu-bar

(fset 'yes-or-no-p 'y-or-n-p)		        ; Easier Options

(setq initial-major-mode 'org-mode)	        ; Start with org-mode
(add-hook 'text-mode-hook 'turn-on-auto-fill)	; Auto-Fill

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;                                  UI SETTINGS                                   ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(setq frame-title-format "%b : %f") 	        ; file : path

(require 'linum)
(setq linum-format 'dynamic)		; right-aligned numbers

(setq-default cursor-type 'bar)	        	; Enable bar cursor
(setq line-number-mode t)		        ; Enable line-number-mode
(setq column-number-mode t)		        ; Enable column-number-mode

(set-face-attribute 'fringe nil :background "#696969")   ; fringe | buffer
(add-to-list 'default-frame-alist '(left-fringe . 2))    ; left fringe 
(add-to-list 'default-frame-alist '(right-fringe . 0))   ; right fringe

(setq scroll-step 1 scroll-conservatively 10000)

(set-face-attribute 'mode-line nil
                      :background "#353644"
                      :foreground "white"
                      :box '(:line-width 8 :color "#353644")
                      :overline nil
                      :underline nil)

  (set-face-attribute 'mode-line-inactive nil
                      :background "#565063"
                      :foreground "white"
                      :box '(:line-width 8 :color "#565063")
                      :overline nil
                      :underline nil)

'(:eval (propertize
         " " 'display
         `((space :align-to (- (+ right right-fringe right-margin)
                               ,(+ 3 (string-width mode-name)))))))

(define-minor-mode minor-mode-blackout-mode
  "Hides minor modes from the mode line."
  t)

(catch 'done
  (mapc (lambda (x)
          (when (and (consp x)
                     (equal (cadr x) '("" minor-mode-alist)))
            (let ((original (copy-sequence x)))
              (setcar x 'minor-mode-blackout-mode)
              (setcdr x (list "" original)))
            (throw 'done t)))
        mode-line-modes))

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;;                 TABBAR                 ;;
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

(use-package tabbar
  :ensure t
  :bind 
  ("<C-S-iso-lefttab>" . tabbar-backward)
  ("<C-tab>" . tabbar-forward)

  :config

  ;; disable buffer groups
  (setq tabbar--buffer-show-groups -1)

  ;; hide *buffers*
  (setq tabbar-buffer-groups-function
        (lambda () (list "All Buffers")))

  (setq tabbar-buffer-list-function
        (lambda ()
          (cl-remove-if
           (lambda(buffer)
             (cl-find (aref (buffer-name buffer) 0) " *"))
           (buffer-list))))

  (set-face-attribute
   'tabbar-default nil
   :background "#353644"
   :foreground "#353644"
   :box '(:line-width 1 :color "#353644" :style nil))
  (set-face-attribute
   'tabbar-unselected nil
   :background "#424355"
   :foreground "white"
   :box '(:line-width 5 :color "#424355" :style nil))
  (set-face-attribute
   'tabbar-selected nil
   :background "#2a2b36"
   :foreground "white"
   :box '(:line-width 5 :color "#2a2b36" :style nil))
  (set-face-attribute
   'tabbar-highlight nil
   :background "white"
   :foreground "#2a2b36"
   :underline nil
   :box '(:line-width 5 :color "white" :style nil))
  (set-face-attribute
   'tabbar-button nil
   :box '(:line-width 1 :color "#353644" :style nil))
  (set-face-attribute
   'tabbar-separator nil
   :background "#353644"
   :height 1.0)

  (custom-set-variables
   '(tabbar-separator (quote (0.2))))

  ;; keep tabs alphabetically sorted
  (defun tabbar-add-tab (tabset object &optional append_ignored)
    "Add to TABSET a tab with value OBJECT if there isn't one there yet.
     If the tab is added, it is added at the beginning of the tab list,
     unless the optional argument APPEND is non-nil, in which case it is
     added at the end."
    (let ((tabs (tabbar-tabs tabset)))
      (if (tabbar-get-tab object tabset)
          tabs
        (let ((tab (tabbar-make-tab object tabset)))
          (tabbar-set-template tabset nil)
          (set tabset (sort (cons tab tabs)
                            (lambda (a b) (string< (buffer-name (car a)) (buffer-name (car b))))))))))

  ;; Change padding of the tabs
  ;; we also need to set separator to avoid overlapping tabs by highlighted tabs
  ;; (custom-set-variables
  ;;  '(tabbar-separator (quote (1.0))))
  (defun tabbar-buffer-tab-label (tab)
    "Return a label for TAB.
  That is, a string used to represent it on the tab bar."
    (let ((label  (if tabbar--buffer-show-groups
                      (format " [%s] " (tabbar-tab-tabset tab))
                    (format " %s " (tabbar-tab-value tab)))))
      ;; Unless the tab bar auto scrolls to keep the selected tab
      ;; visible, shorten the tab label to keep as many tabs as possible
      ;; in the visible area of the tab bar.
      (if tabbar-auto-scroll-flag
          label
        (tabbar-shorten
         label (max 1 (/ (window-width)
                         (length (tabbar-view
                                  (tabbar-current-tabset)))))))))

  (defun px-tabbar-buffer-select-tab (event tab)
    "On mouse EVENT, select TAB."
    (let ((mouse-button (event-basic-type event))
          (buffer (tabbar-tab-value tab)))
      (cond
       ((eq mouse-button 'mouse-2) (with-current-buffer buffer (kill-buffer)))
       ((eq mouse-button 'mouse-3) (pop-to-buffer buffer t))
       (t (switch-to-buffer buffer)))
      (tabbar-buffer-show-groups nil)))

  (defun px-tabbar-buffer-help-on-tab (tab)
    "Return the help string shown when mouse is onto TAB."
    (if tabbar--buffer-show-groups
        (let* ((tabset (tabbar-tab-tabset tab))
               (tab (tabbar-selected-tab tabset)))
          (format "mouse-1: switch to buffer %S in group [%s]"
                  (buffer-name (tabbar-tab-value tab)) tabset))
      (format "\
mouse-1: switch to %S\n\
mouse-2: kill %S\n\
mouse-3: Open %S in another window"
              (buffer-name (tabbar-tab-value tab))
              (buffer-name (tabbar-tab-value tab))
              (buffer-name (tabbar-tab-value tab)))))

  (defun px-tabbar-buffer-groups ()
    "Sort tab groups."
    (list (cond ((or
                  (eq major-mode 'dired-mode)
                  (string-equal "*" (substring (buffer-name) 0 1))) "emacs")
                (t "user"))))

  (setq tabbar-help-on-tab-function 'px-tabbar-buffer-help-on-tab
        tabbar-select-tab-function 'px-tabbar-buffer-select-tab
        tabbar-buffer-groups-function 'px-tabbar-buffer-groups)

  :init
  (tabbar-mode 1))

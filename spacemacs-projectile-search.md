# Add keys to Spacemacs' layers

```elisp
;; Add search functionality into the projectile layer
(spacemacs/declare-prefix "ps" "search")
(spacemacs/set-leader-keys "psr" 'helm-projectile-rg)
(spacemacs/set-leader-keys "pss" 'helm-projectile-ag)
```

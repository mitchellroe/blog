# Show GRUB menu in Fedora

To show the GRUB menu in Fedora, use the following:
```
sudo grub2-editenv - unset menu_auto_hide
# OR
sudo grub2-editenv - set menu_auto_hide=2
```

Then regenerate your grub configuration:
```
sudo grub2-mkconfig -o /etc/grub2-efi.cfg
```

To hide it again:
```
sudo grub2-editenv - set menu_auto_hide=1
sudo grub2-mkconfig -o /etc/grub2-efi.cfg
```

# Set custom locales in GNOME3

## References

All of the following is from [a Unix StackExchange
post](https://unix.stackexchange.com/questions/136920/set-custom-locales-in-gnome3-on-fedora-20#197693).
I am posting it here for posterity. Thank you to dchrome.

----------

## Background Information

I tested the solution on Fedora 21 with GNOME Shell 3.14.4, but I believe it can
be applied to other versions as well.

First thing to understand is that GNOME desktop environment overrides the
system-wide locale definitions and thus is not affected by /etc/locale.conf. In
addition, there are might be applications that have their own locale
configuration and don't use the system or GNOME settings at all. In this guide I
will describe a way to customize the locale settings to your needs and GNOME and
the system will be consistent from the locale perspective.

----------

## Checking Current Locale Status

### System-wide Settings

From [Fedora 21 System Administrator's
Guide](http://docs.fedoraproject.org/en-US/Fedora/21/html/System_Administrators_Guide/ch-System_Locale_and_Keyboard_Configuration.html):

> System-wide locale settings are stored in the `/etc/locale.conf` file, which
> is read at early boot by the **systemd** daemon. The locale settings
> configured in `/etc/locale.conf` are inherited by every service or user,
> unless individual programs or individual users override them.

To see the current locale status we can run:

```
$ localectl status
   System Locale: LANG=en_US.UTF-8
                  LC_NUMERIC=en_US.UTF-8
                  LC_TIME=en_US.UTF-8
                  LC_MONETARY=en_US.UTF-8
                  LC_PAPER=en_US.UTF-8
                  LC_MEASUREMENT=en_US.UTF-8
       VC Keymap: us
      X11 Layout: us
```

### GNOME Settings

```
$ gsettings get org.gnome.system.locale region
'en_US.UTF-8'
```

GNOME has only one setting. By giving a quick look at the [source code of
gnome-control-center](https://git.gnome.org/browse/gnome-control-center/plain/panels/region/cc-region-panel.c?id=GNOME_CONTROL_CENTER_3_14_4)
it seems that when the `set_localed_locale()` function is called, it sets all
the following categories (LC\_TIME, LC\_NUMERIC, LC\_MONETARY, LC\_MEASUREMENT,
LC\_PAPER) to the same one locale defined in `org.gnome.system.locale region`.

Mixing different locale settings seems impossible without creating a custom locale, but fortunately it's not a very complex task.

----------

## Creating Custom Locale

I think the easiest way to explain is by example. In my specific case I wanted
to have a custom locale, primarily based on Hebrew (**he_IL**) but with
LC\_NAME, LC\_MESSAGES from **en_US** and LC\_TIME (with modified
`first_weekday` and `first_workday`) from **en_GB**.

### Grabbing Locale Definition Files

You should have an idea which locales you want to mix. First we need to locate
the related definition files, which can be found in `/usr/share/i18n/locales/`.
Back to my example, I needed the following: **he_IL**, **en_US** and **en_GB**.
I set up a working folder in my home and copied the required files into it:

```
$ cd /usr/share/i18n/locales
$ mkdir -v ~/custom-locale ; cp -v he_IL en_US en_GB ~/custom-locale/
```

### Creating a New Definition File

I decided to call my locale **hc_IL** and took **he_IL** as a basis. The
following lines create a new file `hc_IL` with the contents from `he_IL` and on
the way replace all the occurrences of a string **he_IL** inside the file with
**hc_IL**.

```
$ cd ~/custom-locale/
$ sed 's/he_IL/hc_IL/g' he_IL > hc_IL
```

### Modifying the New Definition File

Now we can customize the new locale to our needs. Open the newly created file
`~/custom-locale/hc_IL` with your favorite text editor. I use **vim** (it has
proper syntax highlighting for locale definition files):

```
$ vim ~/custom-locale/hc_IL
```

For those who haven't chosen their favorite editor yet and vim is not their cup
of tea, can use **gedit** :)

```
$ gedit ~/custom-locale/hc_IL
```

The file structure is not very complicated. Essentially, it is constructed from
sections. From **locale(5)** man pages:

> The locale definition has one part for each locale category. Each part can be
> copied from another existing locale or can be defined from scratch. If the
> category should be copied, the only valid keyword in the definition is copy
> followed by the name of the locale which should be copied.

The notion of **copy** is very useful. It saves time and the resulting file is
clear and concise. For example, instead of copying entire sections around, you
can have:

```
LC_MESSAGES
copy "en_US"
END LC_MESSAGES
```

The complete documentation on a locale definition file can be accessed via:

```
$ man 5 locale
```

Although, if you just want to create a custom locale, which is a mix of existing ones there's no need to understand every detail.

In my case I modified the following categories and keywords:

- **LC_IDENTIFICATION**
  ```
  title      "Custom Hebrew locale"
  source     "mydomain.ws"
  address    "http:////www.mydomain.ws"
  contact    ""
  email      "admin@mydomain.ws"
  tel        ""
  fax        ""
  language   "Hebrew"
  territory  "Israel"
  revision   "1.0"
  date       "2015-04-21"
  ```
  **Note:** All the "*category..*" lines in the **LC_IDENTIFICATION** section
  have been modified during the file creation using `sed`. So we don't need to
  touch them anymore.

- **LC_TIME**

  I copied the complete section from **en_GB** and modified only the lines that
  indicate the first day of week and the first work day:
  ```
  first_weekday 1
  first_workday 1
  ```

- The rest of the categories I left as they are or replaced their content with
  the `copy` directive as in:
  ```
  LC_NAME
  copy "en_US"
  END LC_NAME
  ```

That's it, the definition file is ready. Don't forget to save the file :)

### Compile and Copy the New Locale

Compilation of the new locale is done using the following command as **root** or
using **sudo**. Replace `hc_IL` with your locale:

```
$ sudo localedef  -c -v -i hc_IL -f UTF-8 hc_IL.UTF-8
```

If the compilation is successful the compiled locale data is added to the
archive file `/usr/lib/locale/locale-archive`.

Copy the new locale definition file to the locale definitions directory. Replace
`hc_IL` with your locale:

```
$ sudo cp -v hc_IL /usr/share/i18n/locales/
```

----------

## Activating the New Locale

In this step we want to configure the system and GNOME to use the new locale.

### System-wide Settings

Edit the `/etc/locale.conf` file as **root** and set every line that starts with
`LC_` to your new locale. For example:

```
LANG=en_US.UTF-8
LC_NUMERIC=hc_IL.utf8
LC_TIME=hc_IL.utf8
LC_MONETARY=hc_IL.utf8
LC_PAPER=hc_IL.utf8
LC_MEASUREMENT=hc_IL.utf8
```

### GNOME Settings

To activate the new locale in GNOME run the following command. Replace `hc_IL`
with your locale:

```
$ gsettings set org.gnome.system.locale region "hc_IL.utf8"
```

----------

## Validating the New Settings

The last step is to validate that everything works as expected. To reload all
the settings the easiest for me was to reboot.

- Refer to the section "Checking Current Locale Status" to check that the system
  configuration is correct.

- Use GNOME Shell and locale dependent applications (graphical and console) and
  check that they behave as expected with the new locale. E.g. if you customized
  LC\_TIME you can run the date command to validate the date format:
  ```
  $ date +%x
  21/04/15
  ```

[home](README.md)

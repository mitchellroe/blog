# Password-Protect a PDF

You can password-protect a PDF file using `pdftk`:

```
pdftk input-file.pdf output output-file.pdf user_pw 1234abcd encrypt_128bit
```

This `user_pw` must be entered in order to open and view the PDF.

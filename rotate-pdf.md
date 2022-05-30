# Rotate a PDF

```sh
pdftk 08-file.pdf cat 1-endright output 08-file-rotated.pdf
```

```
pdftk input.pdf cat 1-endsouth output output.pdf
#     \_______/     \___/\___/        \________/
#     input file    range  |          output file
#                         direction
```

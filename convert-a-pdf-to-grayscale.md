# Convert a PDF to Grayscale

## Small File Size

In its simplest form, you can use the following:
```
convert -colorspace GRAY color.pdf gray.pdf
```

## High Quality

To get a high quality (but big) file, use the following:
```
convert -colorspace GRAY -density 400 -quality 100 color.pdf gray.pdf
```

## Other Options

You can also use ghostscript (`gs`) to convert a PDF to grayscale, albeit with some more complex options:
```
gs \
  -sOutputFile=gray.pdf \
  -sDevice=pdfwrite \
  -sColorConversionStrategy=Gray \
  -dProcessColorModel=/DeviceGray \
  -dCompatibilityLevel=1.4 \
  -dNOPAUSE \
  -dBATCH \
  input.pdf
```

## Resources

https://superuser.com/questions/104656/convert-a-pdf-to-greyscale-on-the-command-line-in-floss

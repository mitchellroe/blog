# Enable PDF Creation with `convert`

## Issue

    $ convert scan*.jpg scan.pdf
    convert-im6.q16: attempt to perform an operation not allowed by the security policy `PDF' @ error/constitute.c/IsCoderAuthorized/408.

## Solution

Comment out the restrictions in `/etc/ImageMagick-6/policy.xml`:

```
<!-- disable ghostscript format types -->
  <!--
  <policy domain="coder" rights="none" pattern="PS" />
  <policy domain="coder" rights="none" pattern="EPI" />
  <policy domain="coder" rights="none" pattern="PDF" />
  <policy domain="coder" rights="none" pattern="XPS" />
  -->
  <policy domain="coder" rights="read|write" pattern="PDF,PS" />
</policymap> 
```

## References

<https://cromwell-intl.com/open-source/pdf-not-authorized.html>

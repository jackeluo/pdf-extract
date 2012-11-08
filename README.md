# Node PDF
Node PDF is a set of tools that takes in PDF files and converts them to usable formats for data processing. The library supports both extracting text from searchable pdf files as well as performing OCR on pdfs which are just scanned images of text

[![Build Status](https://travis-ci.org/nisaacson/pdf-extract.png)](https://travis-ci.org/nisaacson/pdf-extract)

## Installation
--------------------
To begin install the module.
`npm install pdf-extract`
After the library is installed you will need the following binaries accessible on your path to process pdfs.

- pdftk
    - pdftk splits multi-page pdf into single pages.
- pdftotext
    - pdftotext is used to extract text out of searchable pdf documents
- ghostscript
    - ghostscript is an ocr preprocessor which convert pdfs to tif files for input into tesseract  
- tesseract
    - tesseract performs the actual ocr on your scanned images


### OSX
To begin on OSX, first make sure you have the homebrew package manager installed. 

**pdftk** is not available in Homebrew. However a gui install is available here. 
[http://www.pdflabs.com/docs/install-pdftk/](http://www.pdflabs.com/docs/install-pdftk/)

**pdftotext** is included as part on the xpdf utilities library. **xpdf** can be installed via homebrew
``` bash
brew install xpdf
```

**ghostscript** can be install via homebrew
``` bash
brew install gs
```

**tesseract** can be installed via homebrew as well
`brew install tesseract`

After tesseract is installed you need to install the alphanumeric config and an updated trained data file
``` bash
cd <root of this project>
cp "./share/eng.traineddata" "/usr/local/Cellar/tesseract/3.01/share/tessdata/eng.traineddata"
cp "lib/alphanumeric" "/usr/local/Cellar/tesseract/3.01/share/tessdata/configs/alphanumeric"
```


### Ubuntu
**pdftk** can be installed directly via apt-get
```bash
apt-get install pdftk
```

**pdftotext** is included in the **poppler-utils** library. To installer poppler-utils execute
``` bash
apt-get install poppler-utils
```

**ghostscript** can be install via apt-get
``` bash
apt-get install ghostscript
```

**tesseract** can be installed via apt-get. Note that unlike the osx install the package is called **tesseract-ocr** on Ubuntu, not **tesseract** 
``` bash
apt-get install tesseract-ocr
```

For the OCR to work, you need to have the tesseract-ocr binaries available on your path. If you only need to handle ASCII characters, the accuracy of the OCR process can be increased by limiting the tesseract output. To do this copy the *alphanumeric* file included with this pdf-extract module into the *tess-data* folder on your system. Also the eng.traineddata included with the standard tesseract-ocr package is out of date. This pdf-extract module provides an up-to-date version which you should copy into the appropriate location on your system
``` bash
cd <root of this project>
cp "./share/eng.traineddata" "/usr/local/Cellar/tesseract/3.01/share/tessdata/eng.traineddata"
cp "lib/alphanumeric" "/usr/local/Cellar/tesseract/3.01/share/tessdata/configs/alphanumeric"
```


### Windows
Not yet tested. If you figure out how to use pdf-extract on windows send me a pull request and I will update the readme accordingly

## Usage
--------------------

### OCR Extract from scanned image
Extract from a pdf file which contains a scanned image and no searchable text 
``` javascript
var inspect = require('eyes').inspector({maxLength:20000});
var pdf_extract = require('pdf-extract');
var absolute_path_to_pdf = '~/Downloads/sample.pdf'
var options = {
  type: 'ocr' // perform ocr to get the text within the scanned image
}
pdf_extract(absolute_path_to_pdf, options, function(err, text_pages) {
  if (err) {
    return callback(err);
  }
  inspect(text_pages, 'ocr output');
  callback(null, text_pages);
});
```

### Text extract from searchable pdf
Extract from a pdf file which contains actual searchable text 
``` javascript
var inspect = require('eyes').inspector({maxLength:20000});
var pdf_extract = require('pdf-extract');
var absolute_path_to_pdf = '~/Downloads/electronic.pdf'
var options = {
  type: 'text'  // extract the actual text in the pdf file
}
pdf_extract(absolute_path_to_pdf, options, function(err, text_pages) {
  if (err) {
    return callback(err);
  }
  inspect(text_pages, 'extracted text pages');
  callback(null, text_pages);
});
```


### Events
When processing, the module will emit various events as they occurr

**page**
Emitted when a page has completed processing. The data passed with this event looks like
``` javascript
var data = {
  text: <extracted text here>,
  index: 2,
  num_pages: 4,
  pdf_path: "~/Downloads/input_pdf_file.pdf",
  single_page_pdf_path: "/tmp/temp_pdf_file2.pdf"
}
```

**error**
Emitted when an error occurs during processing. After this event is emitted processing will stop.
The data passed with this event looks like
```
var data = {
  error: 'no file exists at the path you specified',
  pdf_path: "~/Downloads/input_pdf_file.pdf",
}
```

**complete**
Emitted when all pages have completed processing and the pdf extraction is complete
```
var data = {
  text_pages: <Array of Strings, one per page>,
  pdf_path: "~/Downloads/input_pdf_file.pdf",
  single_page_pdf_file_paths: [
    "/tmp/temp_pdf_file1.pdf",
    "/tmp/temp_pdf_file2.pdf",
    "/tmp/temp_pdf_file3.pdf",
    "/tmp/temp_pdf_file4.pdf",
  ]
}
```


## Tests
--------------------
To test that your system satisfies the needed dependencies and that module is functioning correctly execute the command in the pdf-extract module folder
```
cd <project_root>/node_modules/pdf-extract
npm test
```


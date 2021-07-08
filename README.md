This fork make SetEncryptionMethod public to align with standard
zip lib. Operate header directly.

The work is based on https://github.com/yeka/zip

Available encryption:

```
zip.StandardEncryption
zip.AES128Encryption
zip.AES192Encryption
zip.AES256Encryption
```

## Warning

Zip Standard Encryption isn't actually secure.
Unless you have to work with it, please use AES encryption instead.

## Example Encrypt Zip

```
package main

import (
	"bytes"
	"io"
	"log"
	"os"

	"github.com/mars4myshare/zip"
)

func main() {
	contents := []byte("Hello World")
	fzip, err := os.Create(`./test.zip`)
	if err != nil {
		log.Fatalln(err)
	}
	zipw := zip.NewWriter(fzip)
	defer zipw.Close()

	header, err := zip.FileInfoHeader(info)
	if err != nil {
                log.Fatalf("failed to get the file %s info header: %w", filepath, err)
	}

	// overwrite this with the full path.
	header.Name = filename
	// Change to deflate to gain better compression
	// see http://golang.org/pkg/archive/zip/#pkg-constants
	header.Method = zip.Deflate

        // Specify the utf8
	header.Flags = 1 << 11

        header.SetPassword(pass)

        // change: make this method public
        header.SetEncryptionMethod(zip.StandardEncryption)


	writer, err := zipWriter.CreateHeader(header)

	if err != nil {
		log.Fatal(err)
	}
	_, err = io.Copy(w, bytes.NewReader(contents))
	if err != nil {
		log.Fatal(err)
	}
	zipw.Flush()
}
```

## Example Decrypt Zip

```
package main

import (
	"fmt"
	"io/ioutil"
	"log"

	"github.com/mars4myshare/zip"
)

func main() {
	r, err := zip.OpenReader("encrypted.zip")
	if err != nil {
		log.Fatal(err)
	}
	defer r.Close()

	for _, f := range r.File {
		if f.IsEncrypted() {
			f.SetPassword("12345")
		}

		r, err := f.Open()
		if err != nil {
			log.Fatal(err)
		}

		buf, err := ioutil.ReadAll(r)
		if err != nil {
			log.Fatal(err)
		}
		defer r.Close()

		fmt.Printf("Size of %v: %v byte(s)\n", f.Name, len(buf))
	}
}
```

# echorendering
Template rendering dengab Go/Golang Web Framework [Echo](https://echo.labstack.com/), projek sederhana ini dibuat sebagai latihan menggunakan Echo


![image](https://echo.labstack.com/images/terminal.png)

## Instalasi Package
Pada terminal jalankan perintah berikut :
```Go
go get github.com/labstack/echo
```
atau jika anda meng _clone repository_ 
```bash
go mod tidy
```
## Penjelasan
siapkan package yang akan digunakan dan juga siapkan alias untuk `map[string]interface{}`

```Go
import (
    "github.com/labstack/echo"
    "html/template"
    "io"
    "net/http"
)

type M map[string]interface{} //atau type M map[string]any
```

lalu buat sebuah struct bernama `Renderer`

```Go
type Renderer struct {
    template *template.Template
    debug bool
    location string
}
```

tugas property di atas adalah
  * Property `.template` bertugas untuk melakukan rendering dan parsing template
  * Property `.location` mengarah ke tempat file berada
  * Property `.debug` mengandung nilai bertipe bool
      - jika nilai bertipe `false`, maka parsing template hanya dilakukan sekali saja saat aplikasi di start
      - jika nilai bertipe `true`, maka parsing template dilakukan tiap pengaksesan rute
      
lalu buat fungsi `NewRenderer()`

```Go
func NewRenderer(location string, debug bool) *Renderer {
    tpl := new(Renderer)
    tpl.location = location
    tpl.debug = debug
    tpl.ReloadTemplates()
    return tpl
}
```

Siapkan juga dua buah method untuk struct renderer, yaitu `.ReloadTemplates()` dan `.Render()`

```Go
func (t *Renderer) ReloadTemplates() {
    t.template = template.Must(template.ParseGlob(t.location))
}
```

```Go
func (t *Renderer) Render( w io.Writer, name string, data interface{}, c echo.Context, ) error {
    if t.debug {
      t.ReloadTemplates()
    }
    return t.template.ExecuteTemplate(w, name, data)
}
```

lalu buat echo router, override property renderer, dan siapkan rute untuk halaman tersebut

```Go
func main() {
    e := echo.New()
    e.Renderer = NewRenderer("./*.html", true)
    
    e.GET("/index", func(c echo.Context) error {
      data := M{"message": "Hello World!"}
      return c.Render(http.StatusOK, "index.html", data)
    })
    
    e.Logger.Fatal(e.Start(":9000"))
}
```

Buat file index.html

```HTML
<!DOCTYPE html>
<html>
  <head>
    <title>document</title>
  </head>
  <body>
    Message : {{.message}}!
  </body>
</html>
```
## Menjalankan App

jalankan aplikasi menggunakan

```Go
go run main.go
```
atau
```Go
go build
./namafile
```

dan buka localhost:9000 di browser yang anda gunakan

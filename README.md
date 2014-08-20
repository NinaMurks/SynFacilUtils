SynFacilUtils
=============

Librería para implementación de las funcionalidades típicas de un editor de texto usando el componente SynEdit. Está basado en la librería 'UtilEditSyn' y en el resaltador 'SynFacilCompletion'.

Incluye funciones para el manejo de los formatos de salto de línea más comunes (UNIX, DOS, MAC) y maneja diversas codificaciones de texto como UTF8, win-1252, etc.

Permite la implementación Sencilla de las funciones de Apertura, Cierre, Nuevo archivo, etc.

Maneja las verificaciones de existencia de archivo, o archivo modificado, mostrando los
diálogos apropiados, en cada caso.

Puede manejar diversos mensajes de estado del editor, usando una barra de estado.

También permite guardar un histórico de los archivos abiertos recientemente.

Para usar la librería, se debe incluir obligatoriamente un control TSynEdit (el paquete SynEdit para ser estcritos) y opcionalmente los diálogos TOpenDialog y TSaveDialog, si se desea utilizar las funcionalidades de apertura y grabado de archivos.

La librería requiere los siguientes archivos:

* SynCompletionQ.pas -> Es el reemplazao de la unidad SynCompeltion.pas que viene con Lazarus. Es la versión personalizada para la librería.

* SynFacilHighlighter.pas -> Es el resaltador de sintaxis que usa SynFacilCompletion. Debe ser de la versión 0.9.1 o superior.

* SynFacilCompletion -> Donde se define el resaltador con autocompletado. Se espera que todos los editores, usados por 'SynFacilUtils', usen este resaltdor por defecto.

* SynFacilUtils.pas -> Es la librería donde se define el objeto 'TSynFacilEditor' que es el objeto principal de trabajo. Incluye además utilidades para la implementación de editores de texto.


Para su uso, declarar un objeto Editor y crearlo y destruirlo apropiadamente:

```
edit: TSynFacilEditor;

...

procedure TForm1.FormCreate(Sender: TObject);
begin
  edit := TSynFacilEditor.Create(SynEdit1, 'SinNombre', 'txt');

  //inicia control de paneles
  edit.PanFileSaved := StatusBar1.Panels[0]; //panel para mensaje "Guardado"
  edit.PanCursorPos := StatusBar1.Panels[1];  //panel para la posición del cursor

end;

procedure TForm1.FormDestroy(Sender: TObject);
begin
  edit.Free;
end;
```

El primer parámetro del constructor de TSynFacilEditor, es el editor de trabajo. Solo debe haber un editor asociado a un objeto SynFacilEditor.  

El segundo parámetro del cosntructor, es el nombre del archivo por defecto que se usará cuando se inicie el editor o se cree un nuevo archivo. La librería espera que todo contenido del editor esté asociado a un nombre de archivo, auqnue no se grabe nunca.

El tercer parámetro del cosntructor, es la extensión del archivo por defecto.

Para que el editor sea funcional, se debe interceptarse los eventos para abrir, guardar, etc.

```
procedure TForm1.mnNewClick(Sender: TObject);
begin
  edit.NewFile;
end;

procedure TForm1.mnOpenClick(Sender: TObject);
begin
  OpenDialog1.Filter:='Text files|*.txt|All files|*.*';
  edit.OpenDialog(OpenDialog1);
end;

procedure TForm1.mnSaveClick(Sender: TObject);
begin
  edit.SaveFile;
end;

procedure TForm1.mnSaveAsClick(Sender: TObject);
begin
  edit.SaveAsDialog(SaveDialog1);
end;
```

La idea es pasar el control de las acciones, o eventos de menú al nuevo objeto "TSynFacilEditor" creado, en lugar de manejar al SynEdit.

#Resaltado de sintaxis

Pero la librería SynFacilUtils, está pensada para trabajar con editores con resaltado de sintaxis, usando el resaltador 'SynFacilCompletion'.

Configurar el resaltado de sintaxis es fácil, con SynFacilUtils. Basta con usar el método LoadSyntaxFromFile(), para indicar el archivo de sintaxis que se debe usar.

En el siguiente código, se muestra como iniciar un editor con un archivo XML de sintaxis para el resaltador:

```
procedure TForm1.FormCreate(Sender: TObject);
begin
  edit := TSynFacilEditor.Create(SynEdit1, 'SinNombre', 'c');
  edit.LoadSyntaxFromFile('.\languages\c.xml');
end;
```

Si se desea usar un menú para elegir uno de varios lenguajes, se debe primero tener una carpeta especial donde se encuentren los archivos XML de sintaxis. Luego se debe crear un menú vacío en la aplicación y la configuración con SynFacilUtils, es casi automática:  

```
procedure TForm1.FormCreate(Sender: TObject);
begin
  edit := TSynFacilEditor.Create(SynEdit1, 'SinNombre', 'pas');
  edit.InitMenuLanguages(mnLenguajes, '.\languages');
  edit.LoadSyntaxFromPath;  //para que busque el archivo apropiado
end;
```
Con este código se leerá la carpeta '.\languages', y se creará un ítem en el menú 'mnLenguajes' por cada archivo XML, que se encuentre allí. Además se caragrá el archivo de sintaxis elegido, cuando se seleccione alguno de los ítems del menú.

El método LoadSyntaxFromPath(), carga un archivo de sintaxis, usando la extensión del archivo actual para decidir el archivo a usar. 

IMPORTANTE. 
La librería toma el control de los  eventos OnChange(), OnStatusChange(), OnMouseDown(), OnKeyDown(), OnKeyUp(), OnUTF8KeyPress y OnCommandProcessed().

Si se necesita usar los evento OnChange(), OnMouseDown(), OnKeyDown() u OnKeyUp() se debe usar sus equivalentes TSynFacilEditor.OnEditChange(), TSynFacilEditor.OnMouseDown(),TSynFacilEditor.OnKeyDown() o TSynFacilEditor.OnKeyUp().

Para un ejemplo más completo de uso, ver los proyecto de muestra adjuntos.

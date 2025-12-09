# Libre Office

## Deffining macros in calc

:one: Go to `Tools / Macros / Edit Macros`

- Macros can be set at system level: `My Macros & Dialogs / Standard / Module1`
- File level `<filename>.ods / Standard /`

:two: Insert the following macro:
```vba
Sub ColorearFilasConA

    Dim oDoc As Object

    Dim oHoja As Object

    Dim oCeldas As Object

    Dim oFila As Object

    Dim UltimaFila As Long

    Dim i As Long

 

    oDoc = ThisComponent

    oHoja = oDoc.CurrentController.ActiveSheet

 

    ' Detecta la última fila con datos en la hoja
	oCursor = oHoja.createCursor()
	oCursor.gotoEndOfUsedArea(False)
	ultimaFila = oCursor.RangeAddress.EndRow
 

    For i = 1 To UltimaFila   ' empieza en 1 para saltar títulos si tienes

        oCeldas = oHoja.getCellByPosition(3, i)  ' columna C → índice 2
        oCeldas1 = oHoja.getCellByPosition(3, i) ' columna D -> indice 3

        If UCase(oCeldas1.String) = "DONE" Then

            ' Crear cursor para detectar última columna de datos en esa fila
            oCursorFila = oHoja.createCursorByRange(oHoja.getCellbyPosition(0, i))
            oCursorFila.gotoEndOfUsedArea(True)
            ultimaCol = oCursorFila.RangeAddress.EndColumn
            
            ' Colorear hasta ultima columna con datos
            oRango = oHoja.getCellRangeByPosition(0, i, ultimaCol, i)
			oRango.CellBackColor = RGB(144, 238, 144)
        
        ElseIf UCase(oCeldas.String) = "A" Then

            ' Crear cursor para detectar última columna de datos en esa fila
            oCursorFila = oHoja.createCursorByRange(oHoja.getCellbyPosition(0, i))
            oCursorFila.gotoEndOfUsedArea(True)
            ultimaCol = oCursorFila.RangeAddress.EndColumn
            
            ' Colorear hasta ultima columna con datos
            oRango = oHoja.getCellRangeByPosition(0, i, ultimaCol, i)
			oRango.CellBackColor = RGB(255, 255, 0)

        Else

            ' Quita color si no hay A

            oFila = oHoja.getCellRangeByPosition(0, i, 1024, i)

            oFila.CellBackColor = -1 ' sin color

        End If
    Next i

End Sub
```

You can create new macros in `Modules`

:three: Go to `Tools / Customize / Events` and set the Macro at `Start Application` and `Save Document`
:four: Test it

## Libreoffice replace characters

`Tools / Autocorrect / AutoCorrect Options`

- `Replace` to see / modify the default replacements
- `Localized Options` to modify ''

## Libreoffice Language

`Tools / Language / X / More` -> To select a Language the dictionary should be installed # apt install

## Libreoffice Writer Tables

`Table / Properties / Borders / Padding`


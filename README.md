# VBA-Excel-OneClickSearchingLocationInfo-v.15
一键批量查百度地图&amp;Bing地图
Sub baiduMap()

    Dim url, html, js
    Dim i%, j%
    
    url = ""

    Set html = CreateObject("htmlfile")

    Set js = CreateObject("scriptcontrol")

    js.Language = "jscript"


'----------------选取查询信息所在区域-----------
    quyu = Application.InputBox("为避免网站查询限制，每次查询尽量不要超过500个，过度频繁查询可能无法返回结果" & Chr(13) & Chr(13) & "请选择要查询的地址信息所在单元格区域", "请选择要查询的地址信息", "", Type:=8).Address
    
    Range(quyu).Select
    
    s = Range(quyu).Cells(1, 1).Row
    t = Range(quyu).Rows.Count + s - 1
        
    If s = False Then Exit Sub
    
    If t = False Then Exit Sub
    If t < s Then MsgBox "结束行号不能小行开始行号！": Exit Sub
    Application.ScreenUpdating = False
    Application.EnableEvents = False

'----------------循环查询-----------------

    For s = s To t

        With CreateObject("msxml2.xmlhttp")

            url = "https://map.baidu.com/?newmap=1&reqflag=pcmap&biz=1&from=webmap&da_par=baidu&pcevaname=pc4.1&qt=s&da_src=searchBox.button&wd="
            
            
'-------编码转换-------
            
            Str1 = Cells(s, 1).Value
            
            With CreateObject("scriptcontrol")
            .Language = "javascript"
            Str2 = .eval("encodeURIComponent('" & Str1 & "');")
            
            End With
                        
            url = url & Str2
            
            url = url & "&c=131&src=0&wd2=&pn=0&sug=0&l=12&b=(12575228.9212,2644035.4608000005;12618301.45,2687971.5992)&from=webmap&biz_forward={%22scaler%22:1,%22styles%22:%22pl%22}&sug_forward=&auth=%3DO3RbGcH7yfV4Jg431bVcM8K7gL%40xzVeuxHBBxBzLEEtBnlQADZZz1GgvPUDZYOYIZuVt1cv3uVtPWv3GuLt8BnlQcWlADZZZZZZZZZzWvPYuxt8zv7u%40ZPuLtjADzfiKKvAuexZFTHrwzzvC00dE7&device_ratio=1&tn=B_NORMAL_MAP&nn=0&u_loc=12596793,2623529&ie=utf-8&t=1533132645275"

            .Open "get", url, False

            .send

            js.addcode ("suwenkai = " & .responsetext)

            slen = js.eval("suwenkai.content.length") - 2


            On Error Resume Next
            
           For i = 0 To slen


                Cells(s, 2) = js.eval("suwenkai.content[" & i & "].name")

                Cells(s, 3) = js.eval("suwenkai.content[" & i & "].addr")

                Cells(s, 4) = js.eval("suwenkai.content[" & i & "].tel")
                
            Next
            

        End With
        
    Next

End Sub


Sub BingMap()

    Dim url, html, js
    Dim i%, j%
    
    url = ""

    Set html = CreateObject("htmlfile")

    Set js = CreateObject("scriptcontrol")

    js.Language = "jscript"


'----------------选取查询信息所在区域-----------
    quyu = Application.InputBox("为避免网站查询限制，每次查询尽量不要超过500个，过度频繁查询可能无法返回结果" & Chr(13) & Chr(13) & "请选择要查询的地址信息所在单元格区域", "请选择要查询的地址信息", "", Type:=8).Address
    
    Range(quyu).Select
    
    s = Range(quyu).Cells(1, 1).Row
    t = Range(quyu).Rows.Count + s - 1
        
    If s = False Then Exit Sub
    
    If t = False Then Exit Sub
    If t < s Then MsgBox "结束行号不能小行开始行号！": Exit Sub
    Application.ScreenUpdating = False
    Application.EnableEvents = False

'----------------循环查询-----------------

    For s = s To t

        With CreateObject("msxml2.xmlhttp")

            url = "https://cn.bing.com/maps/overlay?q="
            
            
'-------编码转换-------
            
            Str1 = Cells(s, 1).Value
            
            With CreateObject("scriptcontrol")
            .Language = "javascript"
            Str2 = .eval("encodeURIComponent('" & Str1 & "');")
            
            End With
                        
            url = url & Str2
            
            url = url & "&filters=direction_partner%3A%22maps%22%20tid%3A%22FBEA96CC6B2A40989C2A6CA5C2D47306%22&mapcardtitle=&appid=E18E19EF-764F-41A9-B53E-6E98AE519695&p1=[AplusAnswer]&count=20&ecount=20&first=0&efirst=1&localMapView=30.271807645114265,120.13721036911012,30.26011339339155,120.14394807815553#"
            
            
            
            .Open "get", url, False
            .setRequestHeader "User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.84 Safari/537.36"
            
            .send
            
            text0 = .responsetext
              
            If InStr(1, text0, "searchSuggestionTitle") = 0 Then
            
            On Error Resume Next
            
                        
            text1 = Split(.responsetext, "class=" & Chr(34) & "b_address" & Chr(34) & ">")(1)
            text2 = Split(text1, "</span></li><li><span class=" & Chr(34) & "cbl b_lower" & Chr(34) & ">Phone:</span>")(0)
            
            text3 = Split(text1, "</span></li><li><span class=" & Chr(34) & "cbl b_lower" & Chr(34) & ">Phone:</span>")(1)
            text4 = Split(text3, "</li><li><span class=" & Chr(34) & "cbl b_lower")(0)
            
            Cells(s, 2) = Cells(s, 1)

            Cells(s, 3) = text2

            Cells(s, 4) = text4
                
            
            
            
            Else
            
            
'--------------返回多个搜索结果的时候读取多条记录------------------
           
           With CreateObject("htmlfile")
           
           .write text0
           
           L = .getElementsByTagName("A").Length
           
           
           
           For i = 0 To L - 1
           
           
           On Error Resume Next
           
           Cells(s, 3 * i + 2).Value = Split(.getElementsByTagName("A")(i).innerText, Chr(13))(0)
           Cells(s, 3 * i + 3).Value = Split(.getElementsByTagName("A")(i).innerText, Chr(13))(1)
           Cells(s, 3 * i + 4).Value = Split(.getElementsByTagName("A")(i).innerText, Chr(13))(2)
           
           Next
           
           
           End With
           
            
            End If

        End With
        
    Next

End Sub


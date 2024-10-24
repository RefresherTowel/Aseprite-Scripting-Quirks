# Aseprite-Scripting-Quirks
A place for me to track all the little quirks I run into while scripting in Aseprite

## Modifying dialog settings
If you want one dialog widget to be able to edit another dialog widget, you cannot chain the dialog widgets. For example:

> [!CAUTION]
> Incorrect way of trying to modify widgets within widgets:
> ```
> local dlg = Dialog():radio { id="selection_circle", label = "Selection settings", text = "Circle", selected = false,
>   onclick = function()
>		  dlg:modify {
>			  id = "selection_rectangle",
>			  selected = false
>		  }
>	  end
>   } 
>   :newrow()
>   :radio { id="selection_rectangle", label = "", text = "Rectangle", selected = false,
>     onclick = function()
>	      dlg:modify {
>         id = "selection_circle",
>	        selected = false
>       }
>     end
>   }
> ```

> [!TIP]
> Correct way of trying to modify widgets within widgets:
> ```
> local dlg = Dialog()
> dlg:radio { id="selection_circle", label = "Selection settings", text = "Circle", selected = false,
>   onclick = function()
>	    dlg:modify {
>	      id = "selection_rectangle",
>	      selected = false
>	    }
>	  end
> } 
> dlg:newrow()
> dlg:radio { id="selection_rectangle", label = "", text = "Rectangle", selected = false,
>   onclick = function()
>     dlg:modify {
>       id = "selection_circle",
>       selected = false
>     }
>   end
> ```

## Working with non-rectangular selections
I needed to maintain a copy of a non-rectangular selection while I did some calculations with it (shrunk it down to nothing while keeping track of how many iterations it took) and I could not find a way to simply save a copy of the selection. Everything I tried either ended up being an empty selection, or a rectangular selection that used the bounds of the non-rectangular selection. I managed to get around it by performing undo as many times as I iterated over the selection, which restored it back to what it was before, but I simply got lucky that I didn't need to keep the modifications I made to the selection during the calculations, otherwise the undo would have undid those modifications as well. If anyone knows of a way to copy a non-rectangular selection, please let me know (I'm aware that you can save the selection to disk, but that is impractical in many circumstances).

## Foreground and background colours
For some reason, it took me a long time to track down where these where stoed. The foreground colour is stored under `app.fgColor` and the background under `app.bgColor`.  These are stored as [Color objects](https://www.aseprite.org/api/color#color), not the [unsigned int](https://www.aseprite.org/api/pixelcolor#apppixelcolor) type.

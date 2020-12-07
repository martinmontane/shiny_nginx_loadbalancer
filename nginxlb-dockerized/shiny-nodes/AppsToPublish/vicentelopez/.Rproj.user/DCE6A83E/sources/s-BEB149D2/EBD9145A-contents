createPopups <- function(data,
                         addWhatsapp = TRUE,
                         nombre,
                         tematica,
                         direccion,
                         contactoTel,
                         contactoMail,
                         contactoWeb,
                         captionWhatsapp,
                         descripcion,
                         descripcionCOVID = NULL) {
if(addWhatsapp & !is.null(direccion) & !is.null(captionWhatsapp)) {
  whatsappPopup<- paste("<center> <a href='https://api.whatsapp.com/send?text=",
                        encodeURI(paste("*",data[[nombre]],"*",sep='')),"%0a",encodeURI(direccion),'%0a',encodeURI(captionWhatsapp),"'target='_blank'><i class='fa fa-whatsapp fa-3x'></i></a> </center> ", sep='')

}
  
  
  

  popups <- paste('<b><h3>',data[[nombre]],'</h3></b>
  <div class="leaflet-popup-scrolled">
    <ul class="nav nav-pills mb-2">
      <li class="active"><a data-toggle="pill" href="#pills-home">Descripción</a></li>
      <li><a data-toggle="pill" href="#pills-COVID">COVID-19</a></li>
    </ul>
    <div class="tab-content">
    <div class="tab-pane fade active in" id="pills-home">
      <table class="table">
        <tbody>
          <tr>
            <td>Temática</td>
            <td class="right-col">',paste(lapply(data[[tematica]], function(x) paste(unlist(x),collapse=", ")),'</td>
          </tr>
          <tr>
            <td>¿Qué hacemos?</td>
            <td class="right-col">',data[[descripcion]],'</td>',
        '</tr>
        <tr>
      <td>Teléfono</td>
      <td class="right-col">',data[[contactoTel]],'</td>
    </tr>
    <tr>
      <td>Email</td>
      <td class="right-col">',data[[contactoMail]],'</td>
    </tr>
    <tr>
      <td>Pagina Web</td>
      <td class="right-col"><p><a href="',data[[contactoWeb]],'">' ,data[[contactoWeb]],'</a></p></td>
    </tr>
    <tr>
      <td>Dirección</td>
      <td class="right-col">',direccion,'</td>
    </tr>
    <tr>
      <td colspan="2">',whatsappPopup,'</td>
    </tr>
  </tbody>
</table>
</div>
  <div class="tab-pane fade" id="pills-COVID">
<table class="table">
  <tbody>
    <tr>
      <td>Descripción de actividades durante COVID-19</td>
      <td class="right-col">',data[[descripcionCOVID]],'</td>',
   '</tr>
  </tbody>
</table>
</div>
</div>
</div>
  </div>'
, sep=""), sep="")
  return(popups)
}

cleanJunar <- function(datos_junar) {
  # Pequeño data wrangling que hay que hacer para leer correctamente los datos
  # de longitud y latitud de VL
  datos_junar[,lon:=as.numeric(paste(substr(gsub(lon, pattern = '\\.',replacement= ''),1,3),
                               substr(gsub(lon, pattern = '\\.',replacement= ''),4,nchar(gsub(lon, pattern = '\\.',replacement= ''))),
                               sep='.'))]
  datos_junar[,lat:=as.numeric(paste(substr(gsub(lat, pattern = '\\.',replacement= ''),1,3),
                               substr(gsub(lat, pattern = '\\.',replacement= ''),4,nchar(gsub(lat, pattern = '\\.',replacement= ''))),
                               sep='.'))]
  
  colnames(datos_junar) <- c("Nombre_OSC", "Direccion","Barrio","Presidente_o_responsable",
                       "Tel", "Mail", "Web", "Categoria","lon","lat", "Actividades_organizacion",
                       "Hubo_actividades_durante_covid", "Desc_actividades_durante_covid_19")
  
  # Corrección en la columna de categorías
  datos_junar$categorias <- str_squish(datos_junar$categorias)
  datos_junar$categorias <- str_split(string = datos_junar$Categoria,pattern = ",")
  datos_junar[,categorias:=lapply(categorias,function(x) str_squish(x))]
  datos_junar[,categorias:=lapply(categorias,function(x) str_to_title(x))]
  datos_junar[,categorias:=lapply(categorias,function(x) gsub(pattern = " Y ",replacement = " y ",x = x))]
  datos_junar[,categorias:=lapply(categorias,function(x) ifelse(x %in% c("",NA),"Otros",x))]
  categoriasUnicas <- unique(unlist(datos_junar$categorias))
  
  # Corrección en otras variables
  datos_junar[, Hubo_actividades_durante_covid:=ifelse(Hubo_actividades_durante_covid %in% "SI",TRUE,FALSE)]
  datos_junar[, Desc_actividades_durante_covid_19:=ifelse(is.na(Desc_actividades_durante_covid_19),
                                                    "No declaró actividades durante la pandemia de COVID-19",
                                                    Desc_actividades_durante_covid_19)]
  datos_junar[, Web:=ifelse(!substr(Web,1,4) %in% "http" & !is.na(Web) & !Web %in% "",
                      paste("http://",Web,sep=""),Web)]
  datos_junar[, Web:=ifelse(is.na(Web), "",Web)]
  datos_junar[, Tel:=ifelse(is.na(Tel), "",Tel)]
  datos_junar <- as.data.table(lapply(datos_junar, function(x) ifelse(is.na(x), "", x)))
  
  return(datos_junar)
}

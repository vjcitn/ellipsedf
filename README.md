# ellipsedf

elldf.rda is a data.frame with information about a collection of ellipses in a specific parameterization

Use 
```
df2ellipses = function(datf, npts=100) {
  requireNamespace("sfdep")
  requireNamespace("sf")
  stopifnot(all(c("X_centroid", "Y_centroid", "maj", "min", "angdeg") %in% names(datf)))
  dd = data.matrix(datf[,c("X_centroid", "Y_centroid")])
  cens = lapply(seq_len(nrow(datf)), function(x) sf::st_point(dd[x,]))
  maj = datf$maj
  min = datf$min
  angle_in_deg = datf$angdeg # angle_in_rad/0.01745329
  dd = data.matrix(cbind(dd, angdeg=datf[,"angdeg"])) #angle_in_deg)
  tt = lapply(seq_len(length(cens)), function(x)
    sfdep::st_ellipse(cens[[x]], sx=maj[x], sy=min[x], rotation=angle_in_deg[x], n=npts))
  tttf = lapply(tt, function(x) matrix(unlist(x), nr=npts+1))
  ok = sapply(tttf, function(x) nrow(x)>0)
  sf::st_multilinestring(tttf[which(ok)])
}
```

with elldf to produce



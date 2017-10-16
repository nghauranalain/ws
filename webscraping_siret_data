### Chargement des donnees

options(java.parameters = "-Xmx6000m")
library(xlsx)
siren <- read.xlsx2("list_siren.xlsx", 1, header = TRUE,
                    colClasses = "character")
siren <- siren$siren
str(siren)

### Creer une liste de liens vers les pages societe.com des entreprises
siren_links <- c()

for (i in 1:length(siren)){
        siren_links[i] <- paste("http://www.societe.com/societe/", "*-",
                                siren[i], ".html", sep = "")
        }
head(siren_links)

### Creer une liste de liens vers les pages societe.com des etablissements des
# entreprises (1/2

library(rvest)

siret_links <- c()
for (i in 1:length(siren_links)) {

        # gestion d'erreur
        possible.error <- tryCatch(read_html(siren_links[i]), error = function(e) e)

        if(!inherits(possible.error, "error")){
                # extraction des liens des etablissements
                siret_links <- append(siret_links, read_html(siren_links[i]) %>%
                                              html_nodes("tr+tr .lien") %>%
                                              html_attr("href"))
                etabs_links <- unique(siret_links[grep("/etablissement/",
                                                       siret_links)])
        }
}

length(unique(etabs_links)) # nombre d'etablissements

# liste des liens partiels des etablissements
etabs_links <- as.data.frame(etabs_links)

# liens complets des etablissements vers societe.com (2/2)
# initialiser un vecteur
siret_links <- c()
for (i in etabs_links){
        siret_links[i] <- paste("http://www.societe.com", i, sep = "")
        }

### export des liens
write.xlsx(as.data.frame(siret_links), "siret_links_final.xlsx",
           row.names = FALSE)

### extraction des donnees des etablissements
library(XML)
library(RCurl)
library(httr)

siret_tables <- list()
for (i in 1:length(siret_links)){
        siret_tables[[i]] <- as.matrix(readHTMLTable(
                rawToChar(GET(siret_links[i])$content), which = 1,
                encoding = "UTF-8"))
}

# fusions des donnees des etablissements

siret_tables <- append(siret_tables1, siret_tables2)
length(siret_tables)

data <- Reduce(function(x, y) {
        merge(x, y, by = "Dernière date maj", all = TRUE)
        }, siret_tables)

data.final <- as.data.frame(t(data))

# export
write.xlsx(data.final, "siret_final.xlsx")

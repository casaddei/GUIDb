# GUIDb
Lo scopo di questo progetto è creare un applicazione funzionante su tutti i dispostivi, da pc a telefono. 
Per questo creiamo un progetto MAUI.
Creare attraverso visual studio un nuovo progetto MAUI in linguaggio C#. 

![Immagine 2023-05-16 213140](https://github.com/casaddei/GUIDb/assets/116793299/b09bc1fe-aebc-46d5-ba4b-66c128f8ae43)


A questo punto dobbiamo aggiungere la libreria sql-net.pcl; per farlo clicchiamo su una una parte qualsiasi del progetto e a questo punto 
si aprirà una finestra.
Selezionare gestisci pacchetti NuGet e scegliere la libreria ricercata, ovvero sqlite-net-pcl.
La nostra applicazione dovrà aprire un Db; scarichiamo quindi il Db di prova dal seguente link:https://www.sqlitetutorial.net/wp-content/uploads/2018/03/chinook.zip.

Per quello che riguarda il codice, la problematica più grande è stata nel fatto che sui pc è presente appdata in cui 
si trovano i nostri file, nei dispositivi mobili invece no.

###
    SQLite.SQLiteOpenFlags Flags = SQLite.SQLiteOpenFlags.ReadWrite | SQLite.SQLiteOpenFlags.SharedCache;
 ###
Questo codice serve per fare in modo di puntare alla directory appData anche se non si sa dove si trova. 
###
    if (!File.Exists(targetFile))
 ### 
Successivamente l'if va a controllare se il file esiste; se non esiste tira dritto e non lo sovrascrive, in caso contario si esegue il seguente codice.
###
     {
                using (Stream fileStream = await FileSystem.Current.OpenAppPackageFileAsync("chinook.db"))
                {
                    using (MemoryStream memoryStream = new MemoryStream())
                    {
                        fileStream.CopyTo(memoryStream);
                        File.WriteAllBytes(targetFile, memoryStream.ToArray());
                    }
                }
            }
###
Usiamo l'using e non il try catch perchè in questo modo andiamo a richiamare il distruttore e quindi a staccare il collegamento al Db. 
Questo succede perchè i file da aprire vengono letti attraverso uno stream però SQLite vuole lavorare su un vero e proprio file e quindi copia il file nella cartella AppData.
Async e await permettono di realizzare la programmazione asincrona; in questo modo i thread vengono gestiti automaticamente e quindi si 
eviteranno problemi di lag/blocco dell'interfaccia. 
###
    SQLite.SQLiteOpenFlags Flags = SQLite.SQLiteOpenFlags.ReadWrite | SQLite.SQLiteOpenFlags.SharedCache;
    SQLiteAsyncConnection cn1 = new SQLiteAsyncConnection(targetFile, Flags);
###
A questo punto si crea un collegamento tra target file e il flag, che gestisce la varie modalità per aprire un file. 
###
    List<Artist> tblArtists;
###
Si crea la lista di artisti e poi la query per selezionarli.
###
        List<Artist> tblArtists;
        tblArtists = await cn1.QueryAsync<Artist>("select * from artists where name like 'a%'");
###
Una volta aggiunti i valori ottenuti nella datagrid, bisogna modificare il file xaml.cs.
In MAUI infatti non esiste datagrid ma la si sostituisce ccon una collectionview. Poi si dichiara il template della
collection che permette di far vedere come è fatta la riga e gli si dice che è uno stack layout.

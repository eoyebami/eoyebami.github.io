<h1>Archiving & Compression</h1>

Archiving: is placing a group of files into one (usually files that may ended up being deleted later, similar to AWS Redshift for warehousing) 
  tar: mainly used to archive multiple files (will retain unix metadata)
    flags: 
```
      -c: create archive
      -x: extract archive
      -u: updates archive
      -v: verbose shows details about results of running the tar
      -f: files provide file names
      -z: uses a gzip to compress the archived file (prefix: .tar.gz)
      -b: uses a bzip2 to compress the archived file
      -C: will specify the directory in which you want the contents of the tar file to be dumped into
```   

   ex:
```   
    tar -cvzf [name.tar.gz] file1 file2 ... filen (will archive and gzip all files specified)
       tar -xvzf [name.tar.gz] -C /path/to/directory (will unarchive into directory specified)
       tar -uvzf [archived tar] [updated_files} (will update archive with specified files, if files arent updated, then tar will ignore those files)
       tar -tf [archived tar] (will list all the files that are in this archive)
```

Compression: the files will be reduced to a certain size
  There are 3 times of compressions (compression alogrithm is different)
    gzip: gun zip  
    bzip2: bun zip
    zip: zip (works similarily to the tar archiving, it will compress multiple files into 1)

```
        -x: can be used to exclude files to zip up
        -r: will zip up all files within this folder
        -d: will delete a file from within the zip file
        -fr: will update zip file 
```

      ex:

```  
        zip [zip_file] file1 ... filen -x filen (will compress all the files into a zip file, works like a tar -cvzf, will also exclude specified file)
        zip -d [zip_file] [file_name] (will delete a file from within the zip file 
        zip -fr [zip_file] [updated_files] (will update zip files with specified files)
```

These are functionally equivalent, however decompression speed, compression speen, and availiability may differ. 
zip: used on windows machines (does not preserve all unix metadata (ownership and permissions); alternative is to use tar for archiving
gzip: used on unix machines


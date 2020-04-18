# [juditth.github.io](https://juditth.github.io)

----

* HOME: [Home](https://juditth.github.io)
* Raspberry Pi: [Install Docker to Raspberry Pi B](https://juditth.github.io/raspberryPiB)
* Wordpress: [CZ Wordpress manual](https://juditth.github.io/wordpressManualCz)
* Docker: [Install docker to Raspberry Pi B](https://juditth.github.io/raspberryPiB) \| [Docker play - part 1](https://juditth.github.io/dockerPlayPart1)
* PHP: [Backup of FTP folder](https://juditth.github.io/backupFtpFolder)

----

## Motivation 

Main motivation was to create script able to archive complete structure of the FTP server. As classic says: "Who does not make backups has never lost his data" :-) 

There are many scripts on the internet able to create an archive from the folder, but every of them has some problem. If FTP server is provided by external party and I'm only the maintainer, there is very limited number of options to do, execute, read, write... That's why this script was crated.

Of course the created archives should be stored on the external place, not the same server. But as the beginning this is better than nothing. 

## Complete code 

Let's check the code! 

```
   <?php

   // class archiving the folder
   class ArchiveFtp {

      private $BACKUP_FOLDER = "_backups_";    
      private $DATE = "";
      private $FILE_SIZE_LIMIT = 10485760; //10MB

      private $log_file = "";
      private $log_to_file = false;
      private $source = "";
      private $destination = "";
      private $structure = array();

      // constructor
      function __construct($source, $log_to_file) {
         if (!file_exists($this->BACKUP_FOLDER)) {
               mkdir($this->BACKUP_FOLDER, 0777, true);
         }
         $this->DATE = date("Y-m-d");

         if($log_to_file) {
               $this->log_to_file = true;
               $this->log_file = "./$this->BACKUP_FOLDER/log_$this->DATE.log";
               $file = fopen($this->log_file, "w"); //crate file
               $this->log("log file: $this->log_file");
         }   

         $this->source = $source;
         $this->log("Source: $this->source");

         $this->destination = "./$this->BACKUP_FOLDER/atletikanmnm_wordpress_$this->DATE.zip"; 
         $this->log("Destination: $this->destination");
      }

      // log message to file and output
      private function log($message){
         var_dump($message);
         if ($this->log_to_file){
               file_put_contents($this->log_file, $message.PHP_EOL, FILE_APPEND | LOCK_EX);
         }
      }

      // log one-level array to file and output
      private function log_array($name, $array){
         var_dump($array);
         if ($this->log_to_file){
                  file_put_contents($this->log_file, $name.PHP_EOL, FILE_APPEND | LOCK_EX);            
               foreach($array as $elem){
                  file_put_contents($this->log_file, "  ".$elem.PHP_EOL, FILE_APPEND | LOCK_EX);
               }   
         }
      }

      // create archive with content of the source folder
      function create_archive() {
         $time_start = microtime(true);

         //read structure
         $this->read_folder_structure($this->source);
         $this->structure[] = $this->log_file;  

         //create archive
         $zip = new ZipArchive;
         if ($zip->open($this->destination, ZipArchive::CREATE) === TRUE)
         {
               foreach ($this->structure as $file){
                  $zip->addFile($file);    
               }

               //measure time
               $time_end = microtime(true);
               $time = $time_end-$time_start;
               $this->log("Finished in $time seconds.");
               $this->log_array("Structure:", $this->structure);

               //add log file also
               $this->log("Processed ".sizeof($this->structure)." files.");
               $zip->addFile($this->log_file);    

               $zip->close();
         }        
      }

      //read folder structure
      private function read_folder_structure($dir) {
         if (is_dir($dir)) {
               if ($dh = opendir($dir)) {
                  //read directory content
                  while (($object = readdir($dh)) !== false) {
                     //concatenate path of the object
                     $object_path = $dir."/".$object;
                     //exclude some special files+folders
                     if ($object != "." && $object != ".." && strpos($object_path, ".git") == null && strpos($object_path, $this->BACKUP_FOLDER) == null) {
                           //is directory
                           if (is_dir($object_path)){                            
                              $this->read_folder_structure($object_path);                        
                           //is file
                           } else {
                              $size = filesize($object_path);
                              if ($size > $this->FILE_SIZE_LIMIT){
                                 $this->log("FILE $object_path is to big ($size B, limit is $this->FILE_SIZE_LIMIT B). File not added to archive.");
                              } else {
                                 $this->structure[] = $object_path;                            
                              }
                           }  
                     }
                  }
                  closedir($dh);
               }
         }

         return;
      }
   }   

   // ------------------------------------------------

   $source = "../folder-for-backup";
   $source = ".";
   $archiver = new ArchiveFtp($source, true);
   $archiver->create_archive();

   echo "Done!"

   ?>

```   

### TODOs

There is always the space for improvement. For example:

* store archive on another server
* better logging
* comments
* ...
# Linux Capstone Project : happiness data preprocessing

You work with researchers interested in investigating changes in world happiness from year to year. Your task is to organize the data for a given year. You were provided data on country happiness from different institutes across six continents:
* Africa (AF)
* Asia (AS)
* North America (NA)
* South America (SA)
* Europe (EU)
* Oceania (OC)

To complete this task you will write a script to change the organization `continent_dirs/country_files` to `year_dir/continent_files` (cf below). This will require you to extract data for a given year from all countries and saving it in the appropriate `year/continent` file.

So for example if you extract all data corresponding to year 2015, your original data:

```
happiness_data
    ├─ AF              
    │   ├─ Algeria.csv
    │   ├─ Angola.csv
    │   └─ ...
    ├─ AS
    │   ├─ Afghanistan.csv
    │   ├─ Armenia.csv
    │   └─ ...
    ...
```
should become:
```
happiness_proj
    └─ preproc  
         └─ 2015
             ├─ AF_2015.csv
             ├─ AS_2015.csv
             ├─ ...
             └─ world_2015.csv
    
```
As you can see all data for a given year will be in a unique directory, with continents specified with the continent code in the filename. 

The data were recorded as text files, more precisely Comma Separated Value (CSV) files, i.e. each happiness metrics for a given year are separated by a comma (,). Each line in those files represents measurements for a different year. For example the content of the `happiness_data/AF/Angola.csv` file is:
```
Country name, year, Life Ladder, Log GDP per capita, ...
Angola, 2011, 5.589000701904297, 8.945781707763672, ...
Angola, 2012, 4.360249996185303, 8.991772651672363, ...
Angola, 2013, 3.9371068477630615, 9.004610061645508, ...
...
```
And the cleaned / preprocessed file `happiness_proj/preproc/2015/AF_2015.csv` will look like:
```
Country name, year, Life Ladder, Log GDP per capita, ...
Benin, 2015, 3.624664306640625, 7.988193511962891, ...
Botswana, 2015, 3.761964797973633, 9.608841896057129, ...
Burkina Faso, 2015, 4.4189300537109375, 7.562853813171387, ...
Cameroon, 2015, 5.037964820861816, 8.180439949035645, ...
```

To accomplish your data cleaning / preprocessing your script will rely on using the program `get_year` located inside the `bin` directory within your home dir (e.g. at `/home/ada/bin/get_year`) which extracts the row corresponding to a specific year.

Furthemore you were told to exclude datasets from countries with less than 5 time points (i.e. measurements for fewer than 5 different years) are those were deemed not suitable to study longitudinal changes.

## PART 1
-----
Reorganizing the data and defining the script main variables.
1. Create a directory `happiness_proj`

2. Copy recursively the content of `/home/ada/happiness_data` to a directory `raw` inside `happiness_proj`. The results should be:
```
happiness_proj
    └─ raw
        ├─ AF              
        │   ├─ Algeria.csv
        │   ├─ Angola.csv
        │   └─ ...
        ├─ AS
        │   ├─ Afghanistan.csv
        │   ├─ Armenia.csv
        │   └─ ...
    ...
```

3. Create a new script called `preproc_year_v1` which:
  * assigns to a variable `IN_DIR` the absolute path to your `happiness_proj` directory
  * assigns to a variable `RAW_DIR` the path to the `raw` directory, using the value of the `IN_DIR` variable previously defined
  * assigns to a variable `YEAR` an integer between 2008 and 2021 (choose it yourself)
  * assigns to an array `CSV_FILES` (do not forget quotes `"` `"`) all the CSV files which:
    * are in the subdirectories of `${RAW_DIR}`
    * ends with the `.csv` file extension

    TIP: find the right “globbing” expression and do not forget parentheses to define the array
  * **Within a SINGLE loop**, loop on each item of the resulting array `${CSV_FILES[@]}` and print:
    * the number of time points in that file
      * use `cat`, `|` and `wc -l` within a command expansion `$()`to save the number of time points in a variable `N_TIME_POINTS`, then display the variable value
    * the name of the file (e.g. `Armenia.csv`) to save in a variable `COUNTRY_FILENAME` [TIP: use one string manipulation statement, based on the `#` symbol]
    * the name of the file parent directory (e.g. `AF`) to save in a variable `CONTINENT_CODE` [TIP: use two string manipulation statements: one based on the `%` symbol to create a temporary variable, and another one based on the `#` symbol applied to that temporary variable]

    NOTE: you only need one loop to do everything, so do not create more than one loop

4. Make the script executable and run it. Use `shellcheck preproc_year_v1` to check for errors

## PART 2
---------
Preparing the use of the `get_year` command, without running it yet in the script.
1. Look at the usage of the command `get_year` located in your home `bin` directory (e.g. at `/home/ada/bin/get_year`) by typing the command without arguments
2. If `which get_year` doesn't return the path to that program, this means the directory containing `get_year` is not in your shell global `PATH` variable. In this case, to avoid having to type the full path to the `get_year` program every time you need it:
  * make a backup copy of your account `.bashrc` file (e.g. `cp ~/.bashrc ~/.bashrc_backup`)
  * edit your `.bashrc` file so that to add the path to the directory containing `get_year` to your `PATH` variable
  * update your shell environment by sourcing your `.bashrc` file
  * test that `get_year` is now reachable by your shell (i.e. that it is in your `PATH` variable) by typing `which get_year`
      * If you are working on a Mac and `which get_year` does not work even after updating your `.bash_profile`, try adding the command `source ${HOME}/.bashrc` to the `.profile` file in your home directory, save it, and then start a new terminal to test if it works. Report to Slack if you are stuck here.
3. After reading the usage of `get_year`, test it on a test output file to check it works. Then delete the test output file.
4. Save `preproc_year_v1` as `preproc_year_v2` and edit is so that it also:
  * assigns to a variable `OUT_DIR` the path to a directory named with the year chosen and inside a `preproc` directory itself within `happiness_proj` (e.g. `/home/ada/happiness_proj/preproc/2018` if the year is 2018) [TIP: define `OUT_DIR` using the `IN_DIR` and `YEAR` variables]
  * create the `OUT_DIR` directory if the directory does not exist

      TIPS:
      * use an if-else statement with a unary condition
      * use the `-p` option of `mkdir` if you need to create new directories when one is inside the other
  * loop on each item of `${CSV_FILES[@]}` and:
    * if the file has less than 5 time points, print a message indicating the file is excluded

        TIP: use the variable to which you assigned the number of time points in an `if-else` statement
    * if the file has 5 or more time points:
      * print a message indicating the file is included for preprocessing
      * define a variable `OUT_FILE` with what should be the path where to save the data extracted for that day, e.g. the data from  `AS/Armenia.csv` extracted for year 2018 should be saved in the file `happiness_proj/preproc/2018/AS_2018.csv` [TIP: use variables `OUT_DIR`, `CONTINENT_CODE` and `YEAR` previously defined to define `OUT_FILE`]
      * print to the screen the value of `OUT_FILE`
    * print the command `get_year` which should be used to extract the data at the chosen year with the appropriate arguments (i.e. `echo "get_year -f ..."`) [TIP: make sure you have the right CSV file, output file and year, and that you do not forget to use double quotes `"` `"` appropriately (some CSV files have space in them !)]
5. Make the script executable and run it. Use `shellcheck preproc_day_v2` to check for errors. Check the printed commands make sense.


## PART 3
------
The script is almost ready to be run with `get_year`. However when you tested that program (part 2 task 3), you may have noticed that there is no file header (i.e. the name of the columns are not in the output file). Let's address this.

1. Save `preproc_day_v2` as `preproc_day_v3`
2. All the CSV files have the same header so we can just take the first one to extract that header. After the CSV files array definition but before the for loop:
* create a variable `FIRST_CSV` having for value the path to the first CSV file (TIP: use index 0 to extract the first item of the array)
* create a variable `HEADER` having for value the first line of that first CSV file [TIP: use command expansion `$()` to save the output of a command mixing `cat`, `|` and `head` to extract the first line]
3. Edit the script so that the printed `get_year` command will be run instead of printed (i.e. just remove the appropriate `echo`)
4. Modify your script so that before running `get_year` two different things will happen depending if the output file already exists or not:
* if the output file doesn't exit, write the header to it [TIP: use `echo` with the output redirection `>`] before running `get_year`
* if the output file exists, then simply run `get_year` (the header already exists so no need to add it)
5. Make the script executable and run it. Use `shellcheck preproc_day_v3` to check for errors. Check the output is as described in the project introduction.
6. Do not hesitate to delete your output directory if needed to re-test the script, but please be extremely careful when using the `rm` command on the terminal (you will not be able to recover your files if you make a mistake). Instead of `rm -R <directory>`, you can use `rm -I -R <directory>` to be prompted before confirming file deletion (cf manual with `man rm`).  

## PART 4
------
Create a file including the data for all continents.

1. Save `preproc_day_v3` as `preproc_day_v4`
2. Create a variable `WORLD_OUT_FILE` having for value the name of the file to receive the data from all continents. It should be of the form `happiness_proj/preproc/2018/world_2018.csv` [TIP: use variables `OUT_DIR` and `YEAR` previously defined]. Since this variable is independent of the continent name it should be defined before the for loop.
3. Adding data to the world output file relies on executing a second time `get_year`, and adding the header if `WORLD_OUT_FILE` doesn't exist yet (TIP: repeat the check to add the header and repeat the call to `get_year` in the appropriate place without forgetting to use `WORLD_OUT_FILE` instead of `OUT_FILE`)

## PART 5
------
Allow the script to parse user inputs with `getopts`.

1. Save `preproc_year_v4` as `preproc_year_v5` and use `getopts` for the user to indicate the raw data directory `RAW_DIR`, the `YEAR` and the output directory `OUT_DIR` on the command line so that:
* `RAW_DIR` and `OUT_DIR` are compulsory arguments (the program should exit with error code 1 if the `-i` or `-o` option respectively are not indicated on the command line by the user)
* `YEAR` is optional and has default value 2010 if not indicated on the command line
    * `YEAR` should be between 2010 and 2021 (program should exit if this is not the case)

        TIP: to test two conditions, you can use AND or OR logical operators, which are respectively `&&` and `||`, for example to test that a variable `name` is not equal to `C3PO` or `R2D2` one could write: `if [[ ${name} != "R2D2" && ${name} != "C3PO" ]]; then ...`  (please do not forget the space on each side of all the operators, as well as after `[[` and before `]]`)
2. Edit the program so that it displays a usage function (indicating optional argument in square brackets) when:
* the script is called without arguments
* the script is called with improper parameters (e.g. YEAR=1900)
    
    TIP: copy/paste the content of `/home/ada/getopts_skeleton` or `/home/ada/getopts_skeleton_full` and then adapt it
* Test the script

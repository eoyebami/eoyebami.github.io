<h1>Helm Functions</h1>
* `Templative directives` bash beyon just placing information into templates of your helm chart
  - By using functions, conditionals, vars, etc; we can further templatize and improve our templates in helm; creating a smarter more flexible package for helm to manage

<h2>Functions</h2>
* A function takes in an input, processes it, and outputs in the desired format. Its basically a data transformer
* There are various types of functions that can be used, and I;ve included ones I feel are most commonly used:
  - [String Functions](https://helm.sh/docs/chart_template_guide/function_list/#string-functions):
 
    ```bash
    {% raw %}
    upper: {{ .Values.image.repository | upper }} # converts value to uppercase
    lower: {{ .Values.image.repository | lower }} # converts value to lowercase
    quote: {{ .Values.image.repository | quote }} # places value in quotes
    replace: {{ .Values.image.tag | replace "." "-" }} # replaces characters in value
    shuffle: {{ .Values.random.string | shuffle }} # shuffle characters in value
    contains: {{ .Values.random.string | contains "xxx" }} # returns boolean depending of if value contains specified string
    trim: {{ .Values.image.tag | trim }} # trims empty spaces from value
    required: {{ .Values.image.repository | required "Please provide image repo" }} # requires this value be set, displays string if condition not met
    default: {{ .Values.image.repository | default "nginx" }} # sets default value if not is set in values.yaml
    trimAll: {{ .Values.image.repository | trimAll "/" }} # trims the specified string from anywhere in the value
    trimPrefix: {{ .Values.image.repository | trimPrefix "/" }} # trims specified string from prefix of value
    trimSuffix: {{ .Values.image.repository | trimSuffix "/" }} # trims specified string from suffix of value
    indent: {{ .Values.random.string | indent 4 }} # indents value by 4 spaces
    nindent: {{ .Values.randm.string | nindent 4 }} # prepends new line, then indents 4 spaes
    {% endraw %}
    ```

  - [Date Functions](https://helm.sh/docs/chart_template_guide/function_list/#date-functions):
    * NOTE: Go Lang Time formats that can be used are found [here](https://eoyebami.github.io/posts/helm/2024-04-15-helm-date-formats.html)
  
    ```bash
    {% raw %}
    now: {{ now | quote }} # gives date in "2024-04-15 20:07:54.775125162 +0000 UTC m=+0.052137226" format and adds quotes
    date: {{ now | date "2006-01-02" | quote }} # gives date in +%Y-%m-%d format and adds quotes
    dateInZone: {{ dateInZone "2006-01-02" (now) "MST" }} # gives date in +%Y-%m-%d in specified TZ and quotes
    dateModify: {{ now | dateModify "-1.5h" }} # gives current date and removes 1.5h 
    {% endraw %}
    ```

  - [Dictionary Functions](https://helm.sh/docs/chart_template_guide/function_list/#dictionaries-and-dict-functions):
 
    ```bash
    {% raw %}
    dict: {{ $mydict := dict "name1" "value1" "name2" "value2" "name3" "value 3" }} # creates a map with values specified
    get: {{ get $mydict "name1" }} # retrieves value1 from mydict map
    set: {{ $_ := set $mydict "name4" "value4" }} # sets a new pair to map; $_ traps output
    unset: {{ $_ := unset $mydict "name4" }} # unsets a pair to map; $_ traps output
    unset: {{ $_ := unset $mydict "name4" }} # unsets a pair to map; $_ traps output
    hasKey: {{ haskey $mydict "name1" }} # returns boolean
    {% endraw %}
    ```
    
  - [File Functions](https://helm.sh/docs/chart_template_guide/builtin_objects/):
  
    ```bash
    {% raw %}
    Files.Get: {{ .Files.Get "config.yaml" }} # gets files by name
    Files.GetBytes: {{ .Files.GetBytes "config.yaml" }} # gets content of files as bytes
    Files.Glob: {{ .Files.Glob "config/*" }} # gets list of files that match this pattern
    Files.Lines: {{ .Files.Lines "engines.txt }} # reads file line by line, for functions iterating over lines in a file
    Files.AsSecrets: {{ .Files.AsSecrets "secret.txt" }} # returns file body as base64encoded string
    Files.AsConfig: {{ .Files.AsConfig "engine.json" }} # returns file body as yaml map
    {% endraw %}
    ```

  - [Encoding Functions](https://helm.sh/docs/chart_template_guide/function_list/#encoding-functions):
     
    ```bash
    {% raw %}
    b64enc: {{ .Files.Get "server.key" | b64enc }} # grabs keyfile and encodes 
    {% endraw %}
    ```

  - [List Functions](https://helm.sh/docs/chart_template_guide/function_list/#lists-and-list-functions):

    ```bash
    {% raw %}
    list: {{ $myList := 1 2 3 4 5 }} # creates array and saves as var
    {% endraw %}
    ```

  - [Network Functions](https://helm.sh/docs/chart_template_guide/function_list/#network-functions)
 
    ```bash
    {% raw %}
    getHostByName: {{ getHostByName "www.bashogle.com" }} gets ip of given domain
    ```

  - [Math Functions](https://helm.sh/docs/chart_template_guide/function_list/#math-functions):
    * Math Functions

  - [Float Math Functions](https://helm.sh/docs/chart_template_guide/function_list/#math-functions):
    * Float Math Functions

  - [Logic and Flow Control Functions](https://helm.sh/docs/chart_template_guide/function_list/#logic-and-flow-control-functions):
    * Includes operators that can be used for conditionals

  - [Reflection Functions](https://helm.sh/docs/chart_template_guide/function_list/#reflection-functions):
 
    ```bash
    {% raw %}
    kindOf: {{ kindOf "hello" }} returns object type, like string, slice, int64, and bool
    kindIs: {{ kindIs "ini" 123 }} verifies type of object
    typeOf: {{ typeOf $var }} returns type of var
    typeIs: {{ typeIs "int" $var }} verifies type of object
    deepEqual: {{ deepEqual (list 1 2 3) (list 1 2 3) }} returns var depending on if 2 values are deeply equal
    {% endraw %}
    ```

  - [RegEx Functions](https://helm.sh/docs/chart_template_guide/function_list/#regular-expressions):
 
    ```bash
    {% raw %}
    regexSplit: {{ regexSplit "z+" "pizza" -1 }} # splits string by experssions and returns slices of the string, -1 returns all
    mustregexSplit: {{ regexSplit "z+" "pizza" -1 }} # returns error of template engine if theres a problem
    {% endraw %}
    ```

  - [Semantic Version Functions](https://helm.sh/docs/chart_template_guide/function_list/#semantic-version-functions):
 
    ```bash
    {% raw %}
    semver: {{ $version := semver "1.0.0-SNAPSHOT+123" }} # parses string to semantic version
    *.Major: {{ $version.Major }} # returns major number
    *.Minor: {{ $version.Minor }} # returns minor number
    *.Patch: {{ $version.Patch }} # returns patch number
    *.Prerelease: {{ $version.Prerelease }} # returns prerelease
    *.Metadata: {{ $version.Metadata }} # returns metadata
    *.Original: {{ $version.Original }} # returns original version string
    {% endraw %}
    ```

  - [Type Conversion Functions](https://helm.sh/docs/chart_template_guide/function_list/#type-conversion-functions):
 
    ```bash
    {% raw %}
     int: {{ "8" | int }} convert to int at system's width
     int64: {{ "8" int }} convert to int64
     toDecimal: {{ "10.5" | toDecimal }} {{- $decimalValue := toDecimal "10.5"}} converts string to decimal
     toString: {{ 8 | toString }} converts to string
     toStrings: {{ $mystring := toStrings $mylist }} converts array to list of strings
     toJson: {{ $myjson := toJson $mydict }} converts list, slice, array, dict, or object, to json
     toPrettyJson: {{ $myjson := toPrettyJson $mydict }} converts list, slice, array, dict, or object, to indented json
     toRawJson: {{ $myjson := toRawJson $mydict }} converts list, slice, array, dict, or object, to unescaped json
     toYAML: {{ $myyaml := toYaml $mydict }} converts list, slice, array, dict, or object, to yaml
     fromYaml: {{ $mystring := fromYaml $myyaml }} converts yaml to string, which you can call like $mystring.name
     fromJson: {{ $mystring := fromJson $myjson }} converts json to string, which you can call like $mystring.name
     fromJsonArray: {{ $mystring := fromJsonArray $myjsonarray }} converts json array to list, which you can call similarly to fromJson, range condition will be needed
     fromYamlArray: {{ $mystring := fromYamlArray $myyamlarray }} converts yaml array to list, which you can call similarly to fromYaml, range condition will be needed
    {% endraw %}
    ```

  - [URL Functions](https://helm.sh/docs/chart_template_guide/function_list/#url-functions):
  
    ```bash
    {% raw %}
     urlParse: {{ $url := urlParse "http://admin:secret@server.com:8080/api?list=false#anchor" }} returns dict, which values for scheme, host, path, query, opaque, fragment, and userinfo
    {% endraw %}
    ```

  - [UUID](https://helm.sh/docs/chart_template_guide/function_list/#uuid-functions):
 
    ```bash
    {% raw %}
     uuidv4: {{ $id := uuidv4 }} generates a UUID v4 unique ID and saves it to a var
    {% endraw %}
    ```

  - [Cryptographic & Security Functions](https://helm.sh/docs/chart_template_guide/function_list/#cryptographic-and-security-functions):
     These functions entail encryptions and keygen

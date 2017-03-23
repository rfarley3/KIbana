```
      :::    :::::::::::::::::::::::     :::    ::::    :::    :::
     :+:   :+:     :+:    :+:    :+:  :+: :+:  :+:+:   :+:  :+: :+:
    +:+  +:+      +:+    +:+    +:+ +:+   +:+ :+:+:+  +:+ +:+   +:+
   +#++:++       +#+    +#++:++#+ +#++:++#++:+#+ +:+ +#++#++:++#++:
  +#+  +#+      +#+    +#+    +#++#+     +#++#+  +#+#+#+#+     +#+
 #+#   #+#     #+#    #+#    #+##+#     #+##+#   #+#+##+#     #+#
###    ####################### ###     ######    #######     ###
```

### Kibana: a CLI for Kibana v4 configuration (.kibana index interaction)

* Import/export Kibana dashboards, visualizations, saved searches
    * Compatible with Kibana UI's import/export
    * Can manage config and index-pattern documents
* Refresh field mappings
    * Run periodic refresh to keep Kibana in sync with ES data


## Install

* Use with installing:
    * PyPI: `pip install kibana`
    * Distutils from within repo: `python setup.py install`
    * Either of the above creates a console entry point in your path named `dotkibana`
* Use without installing: `python -m kibana`
    * For any example that follows, replace `dotkibana` with `python -m kibana`


## Usage
```
$ dotkibana --help
usage: [-h] [--status STATUS_IDX] [--refresh REFRESH_IDX]
       [--poll POLL_IDX] [--export EXPORT_OBJ]
       [--import IMPORT_FILE] [--pkg] [--outdir OUTPUT_PATH]
       [--host HOST] [--index INDEX] [--verbose]
       [--transport-class TRANSPORT_CLASS] [--map-ua MAP_UA]

.kibana interaction module

optional arguments:
  -h, --help            show this help message and exit
  --status STATUS_IDX, -s STATUS_IDX
                        exit code is mapping status
  --refresh REFRESH_IDX, -r REFRESH_IDX
                        refreshes mapping
  --poll POLL_IDX, -p POLL_IDX
                        periodically polls (15s) mapping and refreshes if necessary
  --export EXPORT_OBJ, -e EXPORT_OBJ
                        [all|config|dashboard name] to json individual/pkg
                        default: all
  --import IMPORT_FILE, -i IMPORT_FILE
                        import .kibana json obj/pkg
  --pkg                 use pkg mode for import/export
  --outdir OUTPUT_PATH, -o OUTPUT_PATH
                        export only: output file(s) directory
  --host HOST           ES host to use, format ip:port
                        default: 127.0.0.1:9200
  --index INDEX         Kibana index to work on
  --transport-class TRANSPORT_CLASS
                        Elastic search transport class parameter
  --map-ua MAP_UA       Over-ride default http user-agent with a custom,
                        useful when custom authentication is required
  --verbose, -v         show debug output
```


## Mapping Cache Examples/`refreshFields()` Emulation

* Refresh fields' mapping cache for index pattern 'aaa*'
    * `dotkibana --refresh 'aaa*'`
* Check the status of a mapping cache:
    * `dotkibana --status 'aaa*'`
* Periodically enforce mapping cache correctness using ES node 10.0.0.1:
    * `dotkibana --poll 'aaa*' --host 10.0.0.1:9200`


## Import/Export Object Examples

* Get all objects into a single file `all-Pkg.json` under `tmp` under current working directory:
    * `dotkibana --export all --pkg --outdir tmp`
* Get dashboard named 'Big Picture' (and all its vis/search):
    * `dotkibana --export Big-Picture --pkg --outdir tmp`
* Same, but each object in its own file:
    * `dotkibana --export Big-Picture --outdir tmp`

## Transport class and Custom Auth
* In `Elasticsearch` class you can specify transport class to override default http transport.
  it is also helpful to use a custom auth useragent to query .kibana mappings
    * `dotkibana --transport-class custom.AuthTransport --map-ua custom.AuthUa`

## Testing before Deployment

* Use Kibana UI to refresh field mappings
* Verify that the following command indicates equality
    * Replace INDEX_PATT with the index pattern to refresh
```
python -c "import kibana; kibana.DotKibana('INDEX_PATT').mapping.test_cache();"
```

## Release Checklist

* mktmpenv, for both python 2 and 3:
    * `pip install -e .`
    * Do tests
    * `deactivate`
* flake8 kibana/*.py
* Update setup.py: version and download_url to use <tag>
    * Current form is the version number (without the letter v), e.g. 0.3
* Push to GitHub and PyPI
```
git add setup.py
git commit -m 'Bump to version <tag>'
git push
git tag -a <tag> -m '<Annotated tag message>'
git push origin <tag>
python setup.py sdist upload -r pypitest
python setup.py sdist upload -r pypi
```

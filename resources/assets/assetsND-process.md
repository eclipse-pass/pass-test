The details of the process are as follows:
Bring up the PASS Docker stack. Then, increase the search results window size to at least the number of PASS entities
 in the repository. Our test assets image contained 30447 entities, so we set it to 45000:

 `curl -XPUT "http://localhost:9200/pass/_settings" -d '{ "index" : { "max_result_window" : 45000 } }' -H "Content-Type: application/json"`
 
 Now we can pull the assets down in a single query, and save it to a file `results.json` :
 
`curl -X GET "localhost:9200/pass/_search?scroll=1m&size=45000&pretty" -H 'Content-Type: application/json' > results.json

`

Now, clean up the json by picking up the `_source` elements, removing the JournalName_suggest elements, and transforming all attribute names so they don't start with `@`:

`cat results.json | jq '[ .hits.hits[]._source ]' | jq 'del(.. | .journalName_suggest?)' | sed -e 's/\@type/type/g' | sed -e 's/\@id/id/g' | sed -e 's/\@context/context/g'  | jq -c '.[]' > assetsND.json`
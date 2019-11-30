# DigThatLick
[Dig That Lick](http://dig-that-lick.eecs.qmul.ac.uk/index.html) was an international research project concerned with large-scale melodic analysis of jazz improvisations. The central idea of the project was to use licks to trace musical influence and style transmission among jazz musicians. A web interface to our ['100 years of jazz'](https://dig-that-lick.hfm-weimar.de/similarity_search/search?id=1276&group_by=n_gram) dataset will give you an idea of what can be achieved with automatic analysis of a large jazz corpus.

The data collected and created during the project can be found on [OSF](https://osf.io/buxvr/). The code here comes from the metadata/Semantic Web workpackage, for which the data is in [this](https://osf.io/rqk7z/) OSF subproject. In this repository you'll find most of the data that you need to run the scripts, yet larger files are not allowed on GitHub.

The code creates RDF repositories for the datasets in Dig That Lick project. All the code is in Python 3.7.3. To run the scripts, install requirements with . You'll need the `DATA` folder. `dtl.util` is used in all scripts. Adjust the paths at the beginning of each script. For rdf files larger than 100MB running on GPU is recommended: in particular parsing and serialising of an RDF graph is not optimised for speed and takes a long time.

The semantic model which is the basis for the rdf ontologies can be found on in the [Semantic Model](https://osf.io/39q2d/) subproject on the OSF.

## Jazz encylopedia
To create an RDF repository from the CSV file provided with the release and subsequently cleaned during the project, run `JEtoRDF_V6.py`. In this process musicians are matched by name. If you don't want that (have a better way of entity resolution), revert to `JEtoRDF_V4`. It is advised to run this script on a GPU. On my mac it takes about a day to run. You can interrupt the script, it will continue appending to the rdf file after a restart.

You can then perform the following additions:

- `match_leaders.py` will assign each musician whose name is in the bandname as a leader
- `map_instruments.py` will add to each instrument a standard abbreviation label used in the project. 
- `DTLtoLJpeople.py` will look for musician names in the LinkedJazz repository and add owl:sameAs links for Linked Open Data for those musicians that were found. About 10% of our musicians are covered, the links are mostly to DBpedia, but some are MusicBrainz or Library of Congress.
- `JEaddLJrelationships.py` will add relationships between musicians based on inference from the sessions information, such as 'knowsOf', 'hasMet' 'playedTogether', 'inBandTogether', 'bandLeaderOf', etc.

## Lord
`Lord2RDF_V3` creates an RDF repository from the sql database constructed from Lord websites. The sql database is not on GitHub, please get it from [OSF](https://osf.io/cy83b/). Here, again, GPU is highly recommended, it took days on my mac. Additionally, `map_instruments.py`, `DTLtoLJpeople.py` and `JEaddLJrelationships.py` can be applied.

## 100 years of jazz
1. Create an RDF repostory for JE with `JEtoRDF_V6.py`
2. add leaders to it with `match_leaders.py`
3. add fingerprints for JE with `JE_add_short_fprints.py`
4. create an RDF repository from the json files for the (subset of) Illinois dataset with `Ill1000_2RDF.py`
5. add bands to the ILL repositor from the sql database with `Ill1000_add_bands_from_sql.py`. Download the sql database from [OSF](https://osf.io/cy83b/)
6. add leaders to the ILL repository with `match_leaders.py`
7. For both JE and ILL (separately) repositories add DTL instrument labels with `map_instruments.py`
8. add solos information for JE with `JE1000_add_solos.py`
9. add solos for ILL with `Ill1000_add_solos.py`
10. add styles with `add_styles.py`
11. add links to LOD with `DTLtoLJpeople.py`
12. merge JE and ILL repositories with `merge.py`. Musicians, Bands, Tunes and Instruments are merged by name/title. Sessions and performances are not merged since they do not overlap between JE and ILL. Merging is not necessary for the interface to work, just combining the two files would suffice.
13. patch metadata to resolve missing solo performers with `patch_metadata.py`. 
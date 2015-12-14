OBO=http://purl.obolibrary.org/obo/
ONT=bao
SRC=$(ONT)-edit.owl
ROBOT= robot
OWLTOOLS= owltools

test: all_imports


$(ONT).owl: $(SRC) $(MODULE_FILES)  $(ROBOT)
	$(ROBOT) merge -i $<  $(patsubst %,-i %,$(MODULE_FILES)) reason -r ELK -s true annotate -V $(SDG)/releases/`date +%Y-%m-%d`/$(ONT).owl -o $@
$(ONT).obo: $(ONT).owl $(ROBOT)
	$(ROBOT) convert -i $< -o $(ONT).obo


IMPORTS = ro bfo envo chebi pato ncbitaxon
IMPORTS_OWL = $(patsubst %, imports/%_import.owl,$(IMPORTS)) $(patsubst %, imports/%_import.obo,$(IMPORTS))

# Make this target to regenerate ALL
all_imports: $(IMPORTS_OWL)

# Use ROBOT, driven entirely by terms lists NOT from source ontology
imports/%_import.owl: mirror/%.owl imports/%_terms.tsv
	$(ROBOT) extract -i $< -T imports/$*_terms.tsv --method BOT -O $(SDG_IMPORTS_BASE_URI)/$@ -o $@
.PRECIOUS: imports/%_import.owl

imports/%_import.obo: imports/%_import.owl
	$(OWLTOOLS) $(USECAT) $< -o -f obo $@

# clone remote ontology locally, perfoming some excision of relations and annotations
mirror/%.owl: $(SRC)
	$(OWLTOOLS) $(OBO)/$*.owl --remove-annotation-assertions -l -s -d --remove-dangling-annotations  -o $@
.PRECIOUS: mirror/%.owl

release: $(ONT).owl $(ONT).obo
	cp $^ ../ && cp imports/* ../imports

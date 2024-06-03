
build:
	mkdir -p output
	pandoc report.md -t latex --from markdown --template eisvogel --listings --citeproc --bibliography=references.bib -o output/report.pdf
lint:
	hunspell -d en_GB report.md
clean:
	rm -rf output


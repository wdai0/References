# Define your HTML and PDF files here
HTML_FILE = attention.html
PDF_FILE = attention.pdf

# Default rule invoked by make.
# Convert HTML to PDF.
all: $(PDF_FILE)

$(PDF_FILE): $(HTML_FILE)
	pandoc $< -o $@

# Rule for cleaning up the directory.
# Deletes the generated PDF file.
clean:
	rm $(PDF_FILE)


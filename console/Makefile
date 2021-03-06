export PATH := node_modules/.bin:$(PATH)
DIST_PATH ?= ./static/dist
BUCKET_NAME ?= hasura-graphql-engine
VERSION ?= $(shell ../scripts/get-version.sh)

all: deps build gzip-assets gcloud-cp-stable gcloud-set-metadata

deps:
	npm install

ci-deps:
	if [ ! -d "node_modules" ]; then npm ci; fi

build:
	npm run build

test:
	npm run dev & npm run test

gzip-assets:
	gzip $(DIST_PATH)/*.js
	gzip $(DIST_PATH)/*.css
	mv $(DIST_PATH)/main.js.gz $(DIST_PATH)/main.js
	mv $(DIST_PATH)/vendor.js.gz $(DIST_PATH)/vendor.js
	mv $(DIST_PATH)/main.css.gz $(DIST_PATH)/main.css

# to be run inside circle-ci
ci-copy-assets:
	mkdir -p /build/_console_output
	cp $(DIST_PATH)/* /build/_console_output/
	echo "$(VERSION)" > /build/_console_output/version.txt

gcloud-cp-stable:
	gsutil cp -r $(DIST_PATH)/main.css gs://$(BUCKET_NAME)/console/$(VERSION)/main.css
	gsutil cp -r $(DIST_PATH)/main.js gs://$(BUCKET_NAME)/console/$(VERSION)/main.js
	gsutil cp -r $(DIST_PATH)/vendor.js gs://$(BUCKET_NAME)/console/$(VERSION)/vendor.js

gcloud-set-metadata:
	gsutil setmeta -h "Content-Type: application/javascript" gs://$(BUCKET_NAME)/console/$(VERSION)/*.js
	gsutil setmeta -h "Content-Type: text/css" gs://$(BUCKET_NAME)/console/$(VERSION)/*.css
	gsutil setmeta -h "Content-Encoding: gzip" gs://$(BUCKET_NAME)/console/$(VERSION)/*


VERSION=v0.0.1

default: 
	@echo using \"${REPO}\" as repo

cleanup:
	-rm *.xpkg
	-kubectl delete configurations ${REPO}-crossplane-aws-blueprints-s3

build-install: cleanup
	kubectl crossplane build configuration
	kubectl crossplane push configuration ${REPO}/crossplane-aws-blueprints-s3:${VERSION}
	kubectl crossplane install configuration ${REPO}/crossplane-aws-blueprints-s3:${VERSION}

push-marketplace: build-install
	up xpkg push --create ${REPO}/crossplane-aws-blueprints-s3:${VERSION}
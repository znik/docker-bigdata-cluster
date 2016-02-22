IMAGE_NAME := hadoop_master
CONTAINER_NAME := hadoop_master

#useful files
cid_file := build/container_id
host_file := build/hostname

all: $(build)

#** automated rules to write files to maintain some state for other
# docker hosts that need to know where the master is
#
#how to build the image needed
build := build/dockerimage
build: $(build)
$(build): Dockerfile
	mkdir -p build/
	docker build -t $(IMAGE_NAME) .
	touch $@

$(cid_file): $(build)
	docker create --name $(CONTAINER_NAME) $(IMAGE_NAME)
	echo $(CONTAINER_NAME) > $@
	#docker inspect --format '{{ .Config.Hostname }}' `cat build/container_id` > $(host_file)
	#below only works with bridge networking
	docker inspect --format '{{ .NetworkSettings.IPAddress }}' $(CONTAINER_NAME) > $(host_file)
	#docker start -d $(NAME) /etc/bootstrap.sh -d > $@

#how to run:
start: $(cid_file) 
	docker start $(CONTAINER_NAME)

stop:
	-docker stop $(CONTAINER_NAME)
	-rm $(cid_file)

clean: clean_container clean_image
	-rm $(host_file)

clean_container:
	-docker rm -v $(CONTAINER_NAME)
	-rm $(cid_file)

clean_image:
	-docker rmi $(IMAGE_NAME)
	-rm $(build)

#** interactive commands for running a one off
# don't use these 
run_shell: run_shell_hostnetworking

run_shell_hostnetworking:
	docker run -ti --net=host hadoop bash

run_shell_bridgenetworking:
	docker run -ti hadoop bash
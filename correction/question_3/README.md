docker build --tag=hello-outside .

docker run -v $PWD:/toto hello-outside

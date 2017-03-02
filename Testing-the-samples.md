A few examples of Dockerfiles are provided in the `samples/` folder, these images can be used to quickly test `nvidia-docker` on your machine. The samples are not available on the Docker Hub, you will need to build the images locally:
```sh
docker build -t sample:bandwidthTest samples/ubuntu-16.04/bandwidthTest
docker build -t sample:deviceQuery samples/ubuntu-16.04/deviceQuery
docker build -t sample:matrixMulCUBLAS samples/ubuntu-16.04/matrixMulCUBLAS
docker build -t sample:nbody samples/ubuntu-16.04/nbody
docker build -t sample:nvidia-smi samples/ubuntu-16.04/nvidia-smi
docker build -t sample:vectorAdd samples/ubuntu-16.04/vectorAdd
```

Use `nvidia-docker` to test the samples:
```
nvidia-docker run --rm sample:nvidia-smi
nvidia-docker run --rm sample:vectorAdd
nvidia-docker run --rm sample:deviceQuery
nvidia-docker run --rm sample:matrixMulCUBLAS
nvidia-docker run --rm sample:bandwidthTest
```
 
Here is a possible output for a container based on the `sample:deviceQuery` image:
```sh
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "GeForce GTX 980"
  [...]

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 7.5, CUDA Runtime Version = 7.5, NumDevs = 1, Device0 = GeForce GTX 980
Result = PASS
```
ARG PYTHON_VERSION
FROM --platform=linux/amd64 public.ecr.aws/lambda/python:${PYTHON_VERSION}

WORKDIR /tmp
RUN python -m pip install pip -U

# Install system dependencies to compile (numexpr)
RUN yum install -y gcc-c++ python3-devel

# Pin Numpy to 2.2.1 to avoid issues with GCC in later versions
RUN python -m pip install wheel
RUN python -m pip install "numpy==2.2.1"

COPY runtime/requirements.txt requirements.txt
RUN python -m pip install -r requirements.txt "mangum>=0.14,<0.15" -t /asset

# Remove system dependencies
RUN yum remove -y gcc-c++

# Reduce package size and remove useless files
RUN find /asset -type f -name '*.pyc' | while read f; do n=$(echo $f | sed 's/__pycache__\///' | sed 's/.cpython-[0-9]*//'); cp $f $n; done;
RUN find /asset -type d -name '__pycache__' -print0 | xargs -0 rm -rf
RUN find /asset -type f -name '*.py' -print0 | xargs -0 rm -f
RUN find /asset -type d -name 'tests' -print0 | xargs -0 rm -rf
RUN rm -rdf /asset/numpy/doc/ /asset/boto3* /asset/botocore* /asset/bin /asset/geos_license /asset/Misc

COPY runtime/src/*.py /asset/

CMD ["echo", "hello world"]

# Idiom-Detection
This repository contains an ensemble semi-supervised idiom detection model.

**This readme maybe be out of date** please check the repo for an updated one.

# Running the project

## Initial Setup for the first model

Requirements:
* Debian-based (e.g. Debian, Ubuntu, ...) system 
* [conda](https://docs.conda.io/en/latest/) installed

Run the following command to quickly setup the env needed to run our code:
```bash

# setup conda
source ~/miniconda3/etc/profile.d/conda.sh

# create conda env
read -rp "Enter environment name: " env_name
read -rp "Enter python version (e.g. 3.7): " python_version
conda create -yn "$env_name" python="$python_version"
conda activate "$env_name"

# install torch
read -rp "Enter cuda version (e.g. '10.1' or 'none' to avoid installing cuda support): " cuda_version
if [ "$cuda_version" == "none" ]; then
    conda install -y pytorch torchvision cpuonly -c pytorch
else
    conda install -y pytorch torchvision cudatoolkit=$cuda_version -c pytorch
fi

# install python requirements
pip install -r requirements.txt

# download nltk wordnet
python -c "import nltk; nltk.download('wordnet')"

# install java (needed to run raganato eval)
sudo apt-get update && sudo apt-get install -y openjdk-11-jdk

# install xmllint (needed for xml beautification)
sudo apt-get install libxml2-utils

# download raganato framework
read -p "Download raganato framework? [y/N] "
if [[ $REPLY =~ ^[Yy]$ ]]
then

  wget -P data/ http://lcl.uniroma1.it/wsdeval/data/WSD_Evaluation_Framework.zip
  unzip -d data/ data/WSD_Evaluation_Framework.zip
  rm data/WSD_Evaluation_Framework.zip

  # download WNG and WNE
  read -p "Download WNG and WNE? [y/N] "
  if [[ $REPLY =~ ^[Yy]$ ]]
  then
    mkdir data/WSD_Evaluation_Framework/Training_Corpora/WNG/
    wget -P data/WSD_Evaluation_Framework/Training_Corpora/WNG/ https://raw.githubusercontent.com/SapienzaNLP/ewiser/master/res/corpora/training/orig/glosses_main.data.xml
    wget -P data/WSD_Evaluation_Framework/Training_Corpora/WNG/ https://raw.githubusercontent.com/SapienzaNLP/ewiser/master/res/corpora/training/orig/glosses_main.gold.key.txt

    mkdir data/WSD_Evaluation_Framework/Training_Corpora/WNE/
    wget -P data/WSD_Evaluation_Framework/Training_Corpora/WNE/ https://raw.githubusercontent.com/SapienzaNLP/ewiser/master/res/corpora/training/orig/examples.data.xml
    wget -P data/WSD_Evaluation_Framework/Training_Corpora/WNE/ https://raw.githubusercontent.com/SapienzaNLP/ewiser/master/res/corpora/training/orig/examples.gold.key.txt
  fi

fi

```

It's a bash command that will setup a conda environment with everything you need. Just answer the prompts as you proceed.

Libraries that need to be installed:
```
pytorch-lightning==1.1.7
hydra-core==1.1.0.dev5
transformers==4.3.3
nltk==3.4.5
comet-ml==3.10.0
# other
networkx==2.5
black==20.8b1

```

Finally, download the following resources:
* [Wikipedia Freqs](https://drive.google.com/file/d/1WqNKZZFXM1xrVlDUOFSwMBINJGFlbM_l/view?usp=sharing). This is a compressed
  folder containing the files needed to compute the PMI score. Once downloaded, place the file inside *data/* and run:
  ```bash
  cd data/
  tar -xvf pmi.tar.gz
  rm pmi.tar.gz
  cd ..
  ```
* Download the following checkpoints and place them inside the *experiments/* folder (we recommend *experiments/released-ckpts/*):
  * [ConSeC-SemCor](https://drive.google.com/file/d/15__onFMnfGKKyulFxQLStUxdNKiqq-Rn/view?usp=sharing), that is, ConSeC trained on SemCor only (82.0 on ALL);
  * [ConSeC-SemCor+WNGT](https://drive.google.com/file/d/1dwzQ7QDwe8hH4pGBBe-5g4N_BI2eLDfA/view?usp=sharing), that is, ConSeC trained on SemCor + WNGT (83.2 on ALL);
  
 ## Initial setup for the second model
 
 1. `cd` into the `IdiomDetectionDataset` folder.
 2. Download and extract the dataset: [the-eye.eu](https://the-eye.eu/public/AI/pile_preliminary_components/books1.tar.gz)
 3. After downloading and extracting the folder, rename it to `epub.txt`
 4. Then run the `multiprocessing.py` file.
 
 After all this, open the `model.ipynb` to use the models.
 
 # Results
 
 `op.csv` contains the result for the models on idioms and `opn.csv` contains the results fo the models on non-idioms.
 

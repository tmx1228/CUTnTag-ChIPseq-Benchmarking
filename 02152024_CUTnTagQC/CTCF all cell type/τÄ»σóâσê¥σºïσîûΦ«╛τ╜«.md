From vol190 to scratch:

```shell
du -sh /path/to/project/ctcf
mkdir -p /path/to/project
rsync -av /path/to/project/ctcf/ /path/to/project/ctcf/
rsync -av --info=progress2 /path/to/project/ctcf/ /path/to/project/ctcf/
```
From scratch to vol190: (failed: vol190 was out of space)
```shell
rsync -av --info=progress2 /path/to/project/ctcf/ /path/to/project/ctcf/
```
From vol190 to project
```shell
rsync -avh --info=progress2 --partial /path/to/project/ /path/to/project/
```
From scratch to project
```shell
rsync -av --info=progress2 /path/to/project/ctcf/ /path/to/project/ctcf
```
Other

```shell
BAMWD=/path/to/path/to/external_data
cd $BAMWD
ln -s /path/to/path/to/external_data/PE/bamfiles/*.canonical.pos.bam  ./
ln -s /path/to/path/to/external_data/SE/bamfiles/*.canonical.pos.bam ./
ln -s /path/to/path/to/external_data/bamfiles/*.canonical.pos.bam  ./

FRIPWD=/path/to/path/to/external_data
cd $FRIPWD
/path/to/path/to/external_data/macs2/fripscore/all.frip.tsv 
/path/to/path/to/external_data/PE/macs2/fripscore/all.frip.tsv
/path/to/path/to/external_data/SE/macs2/fripscore/all.frip.tsv



R='GSM8302919_CUTTag_CTCF_TTCneg_466_KD'
WD=./
BEDWD=$WD/bedfiles
BAMWD=$WD/bamfiles
BAM=$BAMWD/${R}.q30.canonical.pos.bam
BAM_Ctrl=NA
PEAKWD=$WD/macs2
PEAK_FILE="${PEAKWD}/${R}_peaks.narrowPeak"
FRIPWD=$PEAKWD/fripscore
BWWD=$PEAKWD/bwfiles
/path/to/lab_data/bin/scripts/shell/compute_frip.sh "$R" "$BAM" "$PEAK_FILE" "$FRIPWD"

WD=./
BAMWD=$WD/bamfiles
BAM=${BAMWD}/${R}.q30.canonical.pos.bam
PEAKWD=$WD/sicer
FRIPWD=$PEAKWD/fripscore
PEAK_FILE="${PEAKWD}/${R}.q30.canonical.pos-W50-G150.scoreisland"

/path/to/lab_data/bin/scripts/shell/compute_frip.sh "$R" "$BAM" "$PEAK_FILE" "$FRIPWD"

samtools view -c "$BAM"






cd /path/to/path/to/external_data
ln -s /path/to/path/to/external_data/PE/macs2/*Peak ./
ln -s /path/to/path/to/external_data/PE/sicer/*.scoreisland ./
ln -s /path/to/path/to/external_data/PE/seacr/*.bed ./

ln -s /path/to/path/to/external_data/SE/macs2/*Peak ./
ln -s /path/to/path/to/external_data/SE/sicer/*.scoreisland ./
ln -s /path/to/path/to/external_data/SE/seacr/*.bed ./

ln -s /path/to/path/to/external_data/macs2/*Peak ./
ln -s /path/to/path/to/external_data/sicer/*.scoreisland ./
ln -s /path/to/path/to/external_data/seacr/*.bed ./

```
---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.4.2
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

```python
import glob
```

# all devices

```python
glob.glob('../steelelab/measurement_data/Triton/Mark/*')
```

# Dev 2x1

```python
myfiles = glob.glob(
        '../steelelab/measurement_data/Triton/Mark/2017-05-04_GrapheneJJ_2x1/*/*.dat'
    )
[x.split('/')[-1] for x in myfiles]
```

# Dev 2x2

```python
myfiles = glob.glob(
        '../steelelab/measurement_data/Triton/Mark/180309_1803.2.22_Dev2_gJJcavity_2x2/*/*.dat'
    )
[x.split('/')[-1] for x in myfiles]
```

# Dev 4x2

```python
myfiles = glob.glob(
        '../steelelab/measurement_data/Triton/Mark/2017-07-23_Graphene_4x2/*/*.dat'
    )
[x.split('/')[-1] for x in myfiles]
```

# gSQUID (Hero)

```python
myfiles = glob.glob(
        '../steelelab/measurement_data/Triton/Mark/2017-02-28_Hero_Sample_1x3_in_fridge/*/*.dat'
    )
[x.split('/')[-1] for x in myfiles]
```

```python
myfiles = glob.glob(
        '../steelelab/measurement_data/Triton/Mark/2017-03-02_Hero_Sample_Reborn/*/*.dat'
    )
[x.split('/')[-1] for x in myfiles]
```

```python
[x.split('/')[-1] for x in glob.glob('../steelelab/measurement_data/Triton/Mark/2017-03-02_Hero_Sample_Reborn/*')]
```

```python

```

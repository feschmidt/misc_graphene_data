---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.4.1
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

<!-- #region toc=true -->
<h1>Table of Contents<span class="tocSkip"></span></h1>
<div class="toc"><ul class="toc-item"><li><span><a href="#Libraries" data-toc-modified-id="Libraries-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Libraries</a></span></li><li><span><a href="#Shapiro-8.144GHz" data-toc-modified-id="Shapiro-8.144GHz-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Shapiro 8.144GHz</a></span></li><li><span><a href="#Shapiro-8.198319GHz" data-toc-modified-id="Shapiro-8.198319GHz-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>Shapiro 8.198319GHz</a></span></li><li><span><a href="#Shapiro-5GHz" data-toc-modified-id="Shapiro-5GHz-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>Shapiro 5GHz</a></span></li></ul></div>
<!-- #endregion -->

# Libraries

```python
%run src/basemodules.py
```

```python
devpath = '2017-05-04_GrapheneJJ_2x1/'
```

```python
[x.split('/')[-1] for x in glob.glob(basepath+devpath+'*RFpower*')]
```

```python
[x.split('/')[-1] for x in glob.glob(basepath+devpath+'*RFf*')]
```

# Shapiro 8.144GHz

```python
fname = 'M11_2017_05_09_18.24.39_IV_vs_RFpower_at_cavity/'
datapath = basepath + devpath + fname
```

```python
myfile = sorted(glob.glob(datapath + '/*.dat'))[0]
measfile = sorted(glob.glob(datapath + '/*.py'))[0]
with open(measfile) as f:
    print(f.read())
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
data[0].head()
```

```python
data[-1].head()
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='RF power (dBm)')
mymtx.rotate_ccw()
mymtx.sub_linecut(data[0].shape[0]//2,1)
#mymtx.applystep('flip 1,0')
#mymtx.applystep(f'crop 101,303,0,0')
#mymtx.outlier(100,0)
#mymtx.applystep(f'sub_linecut 100,1')
plt.imshow(mymtx.pmtx,aspect='auto',extent=mymtx.getextents(),cmap='vlag')
plt.colorbar()
```

```python hide_input=false
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap='vlag', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (µV)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
    #filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
    #os.makedirs(os.path.dirname(filename), exist_ok=True)
    #plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
dmtx = copy.deepcopy(mymtx)
dmtx.lowpass(0,1.5)
dmtx.yderiv(1)
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
#    filename = 'plots/'+devpath+'processing_fit_DC_dVdI.png'
#    os.makedirs(os.path.dirname(filename), exist_ok=True)
#    plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

# Shapiro 8.198319GHz

```python
fname = 'M17_2017_05_11_18.31.09_IV_vs_RFpower_at_cavity_again/'
datapath = basepath + devpath + fname
```

```python
myfile = sorted(glob.glob(datapath + '/*.dat'))[0]
measfile = sorted(glob.glob(datapath + '/*.py'))[0]
with open(measfile) as f:
    print(f.read())
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
data[0].head()
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='RF power (dBm)')
mymtx.rotate_ccw()
mymtx.sub_linecut(data[0].shape[0]//2,1)
#mymtx.applystep('flip 1,0')
#mymtx.applystep(f'crop 101,303,0,0')
#mymtx.outlier(100,0)
#mymtx.applystep(f'sub_linecut 100,1')
plt.imshow(mymtx.pmtx,aspect='auto',extent=mymtx.getextents(),cmap='vlag')
plt.colorbar()
```

```python hide_input=false
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap='vlag', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (µV)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
    #filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
    #os.makedirs(os.path.dirname(filename), exist_ok=True)
    #plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
dmtx = copy.deepcopy(mymtx)
dmtx.lowpass(0,0.5)
dmtx.yderiv(1)
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
#    filename = 'plots/'+devpath+'processing_fit_DC_dVdI.png'
#    os.makedirs(os.path.dirname(filename), exist_ok=True)
#    plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 80#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
plt.ylim(-10,10)
#if plotall:
#    filename = 'plots/'+devpath+'processing_fit_DC_dVdI.png'
#    os.makedirs(os.path.dirname(filename), exist_ok=True)
#    plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

```python

```

# Shapiro 5GHz

```python
fname = 'M17_2017_05_12_01.04.31_IV_vs_RFpower_at_cavity_again/'
datapath = basepath + devpath + fname
```

```python
myfile = sorted(glob.glob(datapath + '/*.dat'))[0]
measfile = sorted(glob.glob(datapath + '/*.py'))[0]
with open(measfile) as f:
    print(f.read())
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
data[0].head()
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='RF power (dBm)')
mymtx.rotate_ccw()
mymtx.sub_linecut(data[0].shape[0]//2,1)
#mymtx.applystep('flip 1,0')
#mymtx.applystep(f'crop 101,303,0,0')
#mymtx.outlier(100,0)
#mymtx.applystep(f'sub_linecut 100,1')
plt.imshow(mymtx.pmtx,aspect='auto',extent=mymtx.getextents(),cmap='vlag')
plt.colorbar()
```

```python hide_input=false
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap='vlag', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (µV)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
    #filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
    #os.makedirs(os.path.dirname(filename), exist_ok=True)
    #plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
dmtx = copy.deepcopy(mymtx)
dmtx.lowpass(0,1.5)
dmtx.yderiv(1)
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.tight_layout()
#if plotall:
#    filename = 'plots/'+devpath+'processing_fit_DC_dVdI.png'
#    os.makedirs(os.path.dirname(filename), exist_ok=True)
#    plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

```python

```

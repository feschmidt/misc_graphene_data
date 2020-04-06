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
<div class="toc"><ul class="toc-item"><li><span><a href="#Libraries" data-toc-modified-id="Libraries-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Libraries</a></span></li><li><span><a href="#Shapiro-4.067GHz" data-toc-modified-id="Shapiro-4.067GHz-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Shapiro 4.067GHz</a></span></li><li><span><a href="#Shapiro-4.067GHz-second-msmt" data-toc-modified-id="Shapiro-4.067GHz-second-msmt-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>Shapiro 4.067GHz second msmt</a></span></li><li><span><a href="#Shapiro-8.134GHz" data-toc-modified-id="Shapiro-8.134GHz-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>Shapiro 8.134GHz</a></span></li><li><span><a href="#Shapiro-vs-frequency" data-toc-modified-id="Shapiro-vs-frequency-5"><span class="toc-item-num">5&nbsp;&nbsp;</span>Shapiro vs frequency</a></span></li></ul></div>
<!-- #endregion -->

# Libraries

```python
%run src/basemodules.py
```

```python
devpath = '180309_1803.2.22_Dev2_gJJcavity_2x2/'
```

```python
glob.glob(basepath+devpath+'*RFpower*')
```

```python
glob.glob(basepath+devpath+'*RFf*')
```

# Shapiro 4.067GHz

```python
fname = 'M12_2018_03_14_15.57.41_IV_vs_RFpower_4GHz/'
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
    data[len(data)//4:len(data)*3//4], 'Vmeas (V)', xkey='Iset (V)', ykey='RF power (dBm)')
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
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap='vlag', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
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
vmax = 1000#lims[1]
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

# Shapiro 4.067GHz second msmt

```python
fname = 'M13_2018_03_14_18.31.45_IV_vs_RFpower_4GHz/'
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
    data[len(data)//4:len(data)*3//4], 'Vmeas (V)', xkey='Iset (V)', ykey='RF power (dBm)')
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
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap='vlag', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
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
vmax = 1000#lims[1]
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

# Shapiro 8.134GHz

```python
fname = 'M13_2018_03_14_18.53.30_IV_vs_RFpower_4GHz/'
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
len(data)
```

```python hide_input=false
plt.plot(data[0]['Iset (V)']/1e-6,data[0]['Vmeas (V)']/1e-3)
plt.ylabel('Vmeas (µV)')
plt.xlabel('Iset (µA)')
plt.tight_layout()
#if plotall:
    #filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
    #os.makedirs(os.path.dirname(filename), exist_ok=True)
    #plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

# Shapiro vs frequency

```python
fname = 'M14_2018_03_14_20.41.29_IV_vs_RFfrec_15dBm/'
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
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='RF frec (Hz)')
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
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap='vlag', extent=(
    extents[0]/1e9, extents[1]/1e9, extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF frequency (GHz)')
plt.tight_layout()
#if plotall:
    #filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
    #os.makedirs(os.path.dirname(filename), exist_ok=True)
    #plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
vmtx = copy.deepcopy(mymtx)
vmtx.vi_to_iv(vmin=np.nanmin(mymtx.pmtx.values),vmax=np.nanmax(mymtx.pmtx.values),nbins=300)
vmtx.lowpass(0,1)
```

```python
vv=vmtx.pmtx.values
deltaI = abs(np.diff(mymtx.pmtx.index)[-1])
dvdi = np.gradient(vv)
dvdi
```

```python
deltaI
```

```python
dvdi[0].shape
```

```python
extents = vmtx.getextents()
```

```python
plt.imshow(dvdi[0] / deltaI,
           aspect='auto',
           cmap='binary',
           extent=(extents[0] / 1e9, extents[1] / 1e9,
                   extents[3] / 1e-3,
                   extents[2] / 1e-3),
           vmin=0,
           vmax=3)
cbar = plt.colorbar()
cbar.set_label('dI/dV (a.u.)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('RF frequency (GHz)')
plt.tight_layout()
#if plotall:
#filename = 'plots/'+devpath+'processing_fit_DC_rawdata.png'
#os.makedirs(os.path.dirname(filename), exist_ok=True)
#plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(vmtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = vmtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(vmtx.pmtx/1e-6, aspect='auto', cmap='vlag', extent=(
    extents[0]/1e9, extents[1]/1e9, extents[3]/1e-3, extents[2]/1e-3))#, vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Iset (µA)')
plt.ylabel('Vmeas (µV)')
plt.xlabel('RF frequency (GHz)')
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
dmtx.lowpass(0.5,3)
dmtx.yderiv(1)
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 1000#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0]/1e9, extents[1]/1e9, extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF frequency (GHz)')
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
mymtx.pmtx.shape
```

```python

```

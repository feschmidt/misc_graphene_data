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

<!-- #region toc=true -->
<h1>Table of Contents<span class="tocSkip"></span></h1>
<div class="toc"><ul class="toc-item"><li><span><a href="#Libraries" data-toc-modified-id="Libraries-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Libraries</a></span></li><li><span><a href="#Shapiro-3GHz" data-toc-modified-id="Shapiro-3GHz-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Shapiro 3GHz</a></span></li><li><span><a href="#Shapiro-5GHz" data-toc-modified-id="Shapiro-5GHz-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>Shapiro 5GHz</a></span></li><li><span><a href="#Shapiro-5GHz-second-msmt" data-toc-modified-id="Shapiro-5GHz-second-msmt-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>Shapiro 5GHz second msmt</a></span></li><li><span><a href="#Shapiro-8GHz" data-toc-modified-id="Shapiro-8GHz-5"><span class="toc-item-num">5&nbsp;&nbsp;</span>Shapiro 8GHz</a></span></li><li><span><a href="#Shapiro-8GHz-second-msmt" data-toc-modified-id="Shapiro-8GHz-second-msmt-6"><span class="toc-item-num">6&nbsp;&nbsp;</span>Shapiro 8GHz second msmt</a></span></li><li><span><a href="#Shapiro-10GHz" data-toc-modified-id="Shapiro-10GHz-7"><span class="toc-item-num">7&nbsp;&nbsp;</span>Shapiro 10GHz</a></span></li><li><span><a href="#Shapiro-vs-frequency" data-toc-modified-id="Shapiro-vs-frequency-8"><span class="toc-item-num">8&nbsp;&nbsp;</span>Shapiro vs frequency</a></span><ul class="toc-item"><li><span><a href="#extract-step-height" data-toc-modified-id="extract-step-height-8.1"><span class="toc-item-num">8.1&nbsp;&nbsp;</span>extract step height</a></span><ul class="toc-item"><li><span><a href="#single-linecut" data-toc-modified-id="single-linecut-8.1.1"><span class="toc-item-num">8.1.1&nbsp;&nbsp;</span>single linecut</a></span></li><li><span><a href="#for-all-frequencies" data-toc-modified-id="for-all-frequencies-8.1.2"><span class="toc-item-num">8.1.2&nbsp;&nbsp;</span>for all frequencies</a></span></li></ul></li></ul></li></ul></div>
<!-- #endregion -->

# Libraries

```python
%run src/basemodules.py
```

```python
devpath = '2017-03-02_Hero_Sample_Reborn/'
```

```python
glob.glob(basepath+devpath+'*RFpower*')
```

# Shapiro 3GHz

```python
fname = 'M13_2017_03_12_12.35.45_IV_vs_RFpower_3GHz/'
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
dmtx.lowpass(0.,1.)
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
plt.savefig('plots/processing_Shapiro_Hero_3GHz.png')
plt.show()
plt.close()
```

```python
pickle.dump(dmtx.pmtx,open('pkldump/processing_Shapiro_Hero_3GHz.pkl','wb'))
```

# Shapiro 5GHz

```python
fname = 'M13_2017_03_11_17.34.41_IV_vs_RFpower_5GHz/'
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
plt.savefig('plots/processing_Shapiro_Hero_5GHz.png')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

```python

```

# Shapiro 5GHz second msmt

```python
fname = 'M13_2017_03_11_20.44.25_IV_vs_RFpower_5GHz/'
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
plt.savefig('plots/processing_Shapiro_Hero_5GHz_2.png')
plt.show()
plt.close()
```

```python
pickle.dump(dmtx.pmtx,open('pkldump/processing_Shapiro_Hero_5GHz.pkl','wb'))
```

# Shapiro 8GHz

```python
fname = 'M13_2017_03_12_17.12.37_IV_vs_RFpower_8GHz/'
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
plt.savefig('plots/processing_Shapiro_Hero_8GHz.png')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

# Shapiro 8GHz second msmt

```python
fname = 'M13_2017_03_12_21.09.52_IV_vs_RFpower_8GHz/'
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
plt.savefig('plots/processing_Shapiro_Hero_8GHz_2.png')
plt.show()
plt.close()
```

```python
pickle.dump(dmtx.pmtx,open('pkldump/processing_Shapiro_Hero_8GHz.pkl','wb'))
```

# Shapiro 10GHz

```python
fname = 'M13_2017_03_12_19.25.59_IV_vs_RFpower_10GHz/'
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
plt.savefig('plots/processing_Shapiro_Hero_10GHz.png')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

# Shapiro vs frequency

```python
fname = 'M15_2017_03_13_20.38.32_IV_vs_RFfreq/'
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
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='RF frequency (Hz)')
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
    extents[0]/1e9, extents[1]/1e9, extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (µV)')
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

plt.imshow(dvdi[0]/deltaI,aspect='auto', cmap='binary', extent=(
    extents[0]/1e9, extents[1]/1e9, extents[3]/1e-6, extents[2]/1e-6),vmin=0,vmax=5)
cbar = plt.colorbar()
cbar.set_label('dI/dV (a.u.)')
plt.ylabel('Vmeas (µV)')
plt.xlabel('RF frequency (GHz)')
plt.tight_layout()
plt.savefig('plots/processing_Shapiro_Hero_freq_dIdV.png')
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
    extents[0]/1e9, extents[1]/1e9, extents[2]/1e-6, extents[3]/1e-6))#, vmin=vmin, vmax=vmax)
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
vmax = 100#lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0]/1e9, extents[1]/1e9, extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF frequency (GHz)')
plt.tight_layout()
plt.savefig('plots/processing_Shapiro_Hero_freq_dVdI.png')
plt.show()
plt.close()
```

```python
#pickle.dump(dmtx.pmtx,open('pkldump/Hero_processing_2D_DC.pkl','wb'))
```

```python
mymtx.pmtx.shape
```

## extract step height


### single linecut

```python
import peakutils
```

```python
from scipy.signal import savgol_filter
```

```python
datapath
```

### for all frequencies

```python
mymtx.pmtx.shape
```

```python
freqs, peaks = [],[]

for i in range(mymtx.pmtx.shape[1]):
    lc=mymtx.pmtx.columns[i]
    x,y = mymtx.pmtx.index/1e-6,mymtx.pmtx[lc]/1e-6
    freqs.append(lc) # in Hz

    smooth_y = savgol_filter(y, 21, 3)
    new_x = np.linspace(min(x),max(x),1001)
    new_y = interp1d(x,smooth_y)(new_x)

    # peak finding
    dVdI = np.log(abs(np.gradient(new_x,new_y)))
    indexes = peakutils.indexes(dVdI,min_dist=100)
    peaks.append(new_y[indexes]) # in uV
    
    # test plot
    fig=plt.figure()
    gs=fig.add_gridspec(2,1,hspace=0)
    ax1 = fig.add_subplot(gs[0,0])
    plt.title(f'{i},{lc}')
    plt.plot(y,x)
    plt.plot(new_y,new_x)
    ax1.set_xticklabels([])
    ax2=fig.add_subplot(gs[1,0])
    plt.plot(new_y,dVdI)
    plt.plot(new_y[indexes],dVdI[indexes],'o')
    [theax.set_xlim(-60,60) for theax in [ax1,ax2]]
    plt.show()
    plt.close()
    
```

```python
xdf,ydf = [],[]
```

```python
for ff,pp in zip(freqs,peaks):
    for ppp in pp:
        xdf.append(ff)
        ydf.append(ppp)
```

```python
mydf = pd.DataFrame({'Frequency (Hz)':xdf,'Peakvoltage (uV)':ydf}).set_index('Frequency (Hz)')
```

```python
allfreqs = np.linspace(3e9,10e9,401)
allvolts = Planck/(2*echarge)*allfreqs
```

```python
plt.plot(mydf.index/1e9,mydf['Peakvoltage (uV)'],'o',c='grey',label='data',alpha=0.5)
plt.plot(allfreqs/1e9,allvolts/1e-6,'k--',label='2ef/h')
for i in range(-8,9):
    plt.plot(allfreqs/1e9,i*allvolts/1e-6,'--',c='k')
    
plt.ylim(-80,80)
plt.xlabel('RF frequency (GHz)')
plt.ylabel('Voltage step (µV)')
plt.legend()
plt.savefig('plots/processing_Shapiro_Hero_freq_peaks.png')
```

```python
pickle.dump(
    {
        'RFfreq': mydf.index / 1e9,
        'Vpeak': mydf['Peakvoltage (uV)'],
        'xth': allfreqs / 1e9,
        'yth': allvolts / 1e-6
    }, open('pkldump/processing_Shapiro_Hero_freq.pkl', 'wb'))
```

```python
numlc = mymtx.pmtx.shape[1]
numlc
```

```python
xnew, ynew,fnew=[],[],[]
#for k,i in enumerate(np.arange(0,numlc,50)):
for k,i in enumerate(np.arange(0,numlc,100)):
    lc=mymtx.pmtx.columns[i]
    x,y = mymtx.pmtx.index/1e-6,mymtx.pmtx[lc].values/1e-6
    #smooth_y = savgol_filter(y, 11, 1)
    #new_x = np.linspace(min(x),max(x),1001)
    #new_y = interp1d(x,smooth_y)(new_x)
    plt.plot(x+k,y,label=f'{lc/1e9:.2f} GHz')
    xnew.append(x+k)
    ynew.append(y)
    fnew.append(lc)
plt.legend()
plt.xlabel('Bias current (µA)')
plt.ylabel('Vmeas (µV)')
plt.savefig('plots/processing_Shapiro_Hero_freq_linecuts.png')
```

```python
pickle.dump([xnew, ynew,fnew], open('pkldump/processing_Shapiro_Hero_lc.pkl', 'wb'))
```

```python

```

```python

```

```python

```

```python

```

```python

```

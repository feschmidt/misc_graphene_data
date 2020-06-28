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
<div class="toc"><ul class="toc-item"><li><span><a href="#Libraries" data-toc-modified-id="Libraries-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Libraries</a></span></li><li><span><a href="#2x1-IV_vs_Vg" data-toc-modified-id="2x1-IV_vs_Vg-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>2x1 IV_vs_Vg</a></span><ul class="toc-item"><li><span><a href="#1K" data-toc-modified-id="1K-2.1"><span class="toc-item-num">2.1&nbsp;&nbsp;</span>1K</a></span></li><li><span><a href="#15mK" data-toc-modified-id="15mK-2.2"><span class="toc-item-num">2.2&nbsp;&nbsp;</span>15mK</a></span><ul class="toc-item"><li><span><a href="#trying-IV_to_VI" data-toc-modified-id="trying-IV_to_VI-2.2.1"><span class="toc-item-num">2.2.1&nbsp;&nbsp;</span>trying IV_to_VI</a></span></li></ul></li></ul></li><li><span><a href="#2x2-IV_vs_Vg" data-toc-modified-id="2x2-IV_vs_Vg-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>2x2 IV_vs_Vg</a></span><ul class="toc-item"><li><span><a href="#trying-IV_to_VI" data-toc-modified-id="trying-IV_to_VI-3.1"><span class="toc-item-num">3.1&nbsp;&nbsp;</span>trying IV_to_VI</a></span></li></ul></li><li><span><a href="#2x1-IV_vs_Bfield" data-toc-modified-id="2x1-IV_vs_Bfield-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>2x1 IV_vs_Bfield</a></span><ul class="toc-item"><li><span><a href="#trying-IV_to_VI" data-toc-modified-id="trying-IV_to_VI-4.1"><span class="toc-item-num">4.1&nbsp;&nbsp;</span>trying IV_to_VI</a></span></li></ul></li></ul></div>
<!-- #endregion -->

# Libraries

```python
%run src/basemodules.py
```

# 2x1 IV_vs_Vg


## 1K

```python
# Reading in the raw data
devpath = '2017-05-04_GrapheneJJ_2x1/'
fname = 'M44_2017_06_06_21.02.59_IV_vs_Vg_1K'
datapath = basepath+devpath+fname
```

```python
myfile = sorted(glob.glob(datapath+'/*.dat'))[0]
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
#mymtx.applystep('sub_linecut 300,1')
#mymtx.applystep('scale_data 1e-3')
# mymtx.applystep('outlier 905,1')
# mymtx.applystep('outlier 202,1')
# mymtx.applystep('outlier 28,1')
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = -1 # lims[0]
vmax = 1 # lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
#mymtx.applystep('lowpass 0,1')
mymtx.applystep('yderiv 1')
```

```python
# plot dVdI

wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(mymtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0 # lims[0]
vmax = 500 # lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
pickle.dump(mymtx.pmtx,open('pkldump/processing_DC_2x1_Vg_1K.pkl','wb'))
```

## 15mK

```python
# Reading in the raw data
devpath = '2017-05-04_GrapheneJJ_2x1/'
fname = 'M33_2017_05_30_19.43.53_IV_vs_Vg_s4c_m30Vto30V_fine_wide_FINALLY'
datapath = basepath+devpath+fname
```

```python
myfile = sorted(glob.glob(datapath+'/*.dat'))[0]
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
mymtx.applystep('sub_linecut 500,1')
mymtx.applystep('scale_data 1e-3')
#mymtx.applystep('sub_linecut 300,1')
#mymtx.applystep('scale_data 1e-3')
# mymtx.applystep('outlier 905,1')
# mymtx.applystep('outlier 202,1')
# mymtx.applystep('outlier 28,1')
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = -1 # lims[0]
vmax = 1 # lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
mymtx.applystep('lowpass 0,1')
mymtx.applystep('yderiv 1')
```

```python
# plot dVdI

wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(mymtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0 # lims[0]
vmax = 500 # lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
pickle.dump(mymtx.pmtx,open('pkldump/processing_DC_2x1_Vg_15mK.pkl','wb'))
```

### trying IV_to_VI

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
mymtx.applystep('sub_linecut 500,1')
mymtx.applystep('scale_data 1e-3')
#mymtx.applystep('sub_linecut 300,1')
#mymtx.applystep('scale_data 1e-3')
# mymtx.applystep('outlier 905,1')
# mymtx.applystep('outlier 202,1')
# mymtx.applystep('outlier 28,1')
```

```python
dI = np.diff(mymtx.pmtx.axes[0])[0]
dI
```

```python
#mymtx.pmtx.head()
```

```python
vmin = np.min(mymtx.pmtx.values)
vmax = np.max(mymtx.pmtx.values)
nbins=1000
```

```python
mymtx.vi_to_iv(vmin=vmin,vmax=vmax,nbins=nbins)
mymtx.flip(y=1)
```

```python
#mymtx.pmtx.head()
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('Iset (µA)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Vgate (V)')
plt.show()
plt.close()
```

```python
mydf = mymtx.pmtx
```

```python
myg = np.gradient(mydf)[0]/dI
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(myg, (wbval[0], 100 - wbval[1]))
vmin = -0#lims[0]
vmax = 10#lims[1]
extents = mymtx.getextents()

plt.imshow(myg, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('dI/dV (a.u.)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Vgate (V)')
#plt.ylim(-1,0)
plt.show()
plt.close()
```

# 2x2 IV_vs_Vg

```python
devpath = '180309_1803.2.22_Dev2_gJJcavity_2x2/'
fname = 'M8_2018_03_12_19.05.46_IV_vs_Vg_14mK_fine'
datapath = basepath+devpath+fname
```

```python
myfile = sorted(glob.glob(datapath+'/*.dat'))[0]
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
mymtx.applystep('sub_linecut 500,1')
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = -1 # lims[0]
vmax = 1 # lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
mymtx.applystep('lowpass 0,1')
mymtx.applystep('yderiv 1')
```

```python
# plot dVdI

wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(mymtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0 # lims[0]
vmax = 500 # lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
pickle.dump(mymtx.pmtx,open('pkldump/processing_DC_2x2_Vg.pkl','wb'))
```

## trying IV_to_VI

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
mymtx.applystep('sub_linecut 500,1')
```

```python
dI = np.diff(mymtx.pmtx.axes[0])[0]
dI
```

```python
#mymtx.pmtx.head()
```

```python
vmin = np.min(mymtx.pmtx.values)
vmax = np.max(mymtx.pmtx.values)
nbins=1000
```

```python
mymtx.vi_to_iv(vmin=vmin,vmax=vmax,nbins=nbins)
mymtx.flip(y=1)
```

```python
#mymtx.pmtx.head()
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('Iset (µA)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Vgate (V)')
plt.show()
plt.close()
```

```python
mydf = mymtx.pmtx
```

```python
myg = np.gradient(mydf)[0]/dI
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(myg, (wbval[0], 100 - wbval[1]))
vmin = -0#lims[0]
vmax = 10#lims[1]
extents = mymtx.getextents()

plt.imshow(myg, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('dI/dV (a.u.)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Vgate (V)')
#plt.ylim(-.2,0)
plt.show()
plt.close()
```

# 2x1 IV_vs_Bfield

```python
# Reading in the raw data
devpath = '2017-05-04_GrapheneJJ_2x1/'
fname = 'M14_2017_05_10_15.32.29_IV_vs_magnet_isource'
datapath = basepath+devpath+fname
```

```python
myfile = sorted(glob.glob(datapath+'/*.dat'))[0]
```

```python
# Reading in the raw data

print(myfile)
data = stlabutils.readdata.readdat(myfile)
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='B (T)')
mymtx.applystep('rotate_ccw')
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.percentile(mymtx.pmtx.values/1e-3, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

# Plotting the raw data
# Full overview
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (uA)')
plt.xlabel('Bfield (T)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
#mymtx.applystep('lowpass 0,1')
mymtx.applystep('yderiv 1')
```

```python
# plot dVdI

wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(mymtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (uA)')
plt.xlabel('Bfield (T)')
plt.title('Full overview')
plt.tight_layout()
plt.show()
plt.close()
```

```python
pickle.dump(mymtx.pmtx,open('pkldump/processing_DC_2x1_Bfield_15mK.pkl','wb'))
```

## trying IV_to_VI

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='B (T)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('sub_linecut 100,1')
```

```python
dI = np.diff(mymtx.pmtx.axes[0])[0]
dI
```

```python
#mymtx.pmtx.head()
```

```python
vmin = np.min(mymtx.pmtx.values)
vmax = np.max(mymtx.pmtx.values)
nbins=1000
```

```python
mymtx.vi_to_iv(vmin=vmin,vmax=vmax,nbins=nbins)
mymtx.flip(y=1)
```

```python
#mymtx.pmtx.head()
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(mymtx.pmtx.values/1e-6, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = mymtx.getextents()

plt.imshow(mymtx.pmtx/1e-6, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('Iset (µA)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Bfield (T)')
plt.show()
plt.close()
```

```python
mydf = mymtx.pmtx
```

```python
myg = np.gradient(mydf)[0]/dI
```

```python
wbval = (0.1, 0.1)
cmap = 'RdBu_r'
lims = np.nanpercentile(myg, (wbval[0], 100 - wbval[1]))
vmin = -1#lims[0]
vmax = 1#lims[1]
extents = mymtx.getextents()

plt.imshow(myg, aspect='auto', cmap=cmap, extent=(
    extents[0], extents[1], extents[2]/1e-3, extents[3]/1e-3), vmin=vmin, vmax=vmax)
cbar = plt.colorbar()
cbar.set_label('dI/dV (A/V)')
plt.ylabel('Vmeas (mV)')
plt.xlabel('Bfield (T)')
plt.show()
plt.close()
```

```python

```

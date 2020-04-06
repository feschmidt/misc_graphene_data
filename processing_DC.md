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
<div class="toc"><ul class="toc-item"><li><span><a href="#Libraries" data-toc-modified-id="Libraries-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Libraries</a></span></li><li><span><a href="#IV_vs_Vg" data-toc-modified-id="IV_vs_Vg-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>IV_vs_Vg</a></span></li><li><span><a href="#IV_vs_Bfield" data-toc-modified-id="IV_vs_Bfield-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>IV_vs_Bfield</a></span></li></ul></div>
<!-- #endregion -->

# Libraries

```python
%run src/basemodules.py
```

```python
devpath = '2017-03-02_Hero_Sample_Reborn/'
```

# IV_vs_Vg

```python
fname = 'M6_2017_03_07_11.19.49_IV_vs_vg_CorrectIPolarity_isource'
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

```python
datashape = data[0].shape
datashape
```

```python
data[len(data) // 4 * 3].head()
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data[len(data)//4:len(data)*3//4], 'Vmeas (V)', xkey='Iset (V)', ykey='Vgate (V)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
mymtx.applystep(f'crop 101,303,0,0')
mymtx.outlier(100,0)
mymtx.applystep(f'sub_linecut 100,1')
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
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
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
#dmtx.applystep('lowpass 0,1')
dmtx.applystep('yderiv 1')
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = lims[0]
vmax = lims[1]
extents = dmtx.getextents()

plt.imshow(dmtx.pmtx, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (uA)')
plt.xlabel('Gate voltage (V)')
plt.title('Full overview')
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
forbidden = []#173,905,998,1005,1172]
```

```python hide_input=false
vgate, rn, iswitch, itrap = [], [], [], []
di = []

for i,vals in enumerate(mymtx.pmtx.T.values):

    Vg = mymtx.pmtx.columns[i]
    Iset = mymtx.pmtx.index
    Vmeas = vals
    #Vmeas = Vmeas - Vmeas[len(Vmeas)//2] # remove voltage offset
    Rmeas = Vmeas/Iset
    dVdI = np.gradient(Vmeas,Iset)
    tridx = np.argmax(dVdI[len(dVdI)//2:]) + len(dVdI)//2
    swidx = np.argmax(dVdI[:len(dVdI)//2])

    Isw = Iset[swidx]
    Itr = abs(Iset[tridx])
    dI = np.diff(Iset)[0] # uncertainty in current: step size

    if i not in forbidden:
        vgate.append(Vg)
        rn.append(Rmeas[0])
        iswitch.append(Isw)
        itrap.append(Itr)
        di.append(dI)
    else:
        vgate.append(np.nan)
        rn.append(np.nan)
        iswitch.append(np.nan)
        itrap.append(np.nan)
        di.append(np.nan)
        
vgate = np.array(vgate)
rn = np.array(rn)
iswitch = np.array(iswitch)
itrap = np.array(itrap)
di = np.array(di)

fig,(ax1,ax2,ax3) = plt.subplots(1,3,figsize=(15,4))
plt.sca(ax1)
plt.plot(vgate,rn)
# plt.ylim(7.2,8.2)
plt.xlabel('Vgate (V)')
plt.ylabel('Rn (Ohm)')
plt.sca(ax2)
plt.errorbar(vgate,iswitch/1e-6,di/1e-6,label='Iswitch',fmt='.')
plt.errorbar(vgate,-itrap/1e-6,di/1e-6,label='Itrap',fmt='.')
plt.ylabel('Critical currents (µA)')
plt.xlabel('Vgate (V)')
plt.legend()
plt.sca(ax3)
[plt.axvline(data[ff]['Vgate (V)'].values[0],c='C1') for ff in forbidden]
plt.errorbar(vgate,iswitch*rn/1e-6,di*rn/1e-6,fmt='.')
plt.ylabel('IcRn product (µV)')
plt.xlabel('Vgate (V)')
plt.tight_layout()
#filename = 'plots/'+devpath+'processing_fit_DC_fitdata.png'
#os.makedirs(os.path.dirname(filename), exist_ok=True)
#plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
pkldump = {
    'Vgate (V)':vgate,
    'Rn (Ohm)':rn,
    'Iswitch (A)':iswitch,
    'Itrap (A)':itrap,
    'dI (A)':di
}
```

```python
#pickle.dump(pkldump,open('pkldump/Hero_processing_fit_DC.pkl','wb'))
```

```python
mymtx.pmtx.shape
```

```python
xnew, ynew = [],[]
for i in range(121):
    lc = mymtx.pmtx.columns[i]
    xx = mymtx.pmtx[lc]/1e-6
    yy = mymtx.pmtx.index/1e-6
    xnew.append(xx)
    ynew.append(yy)
    plt.plot(xx,yy,ls='-',c='grey',alpha=0.5)
plt.xlim(-75,1)
plt.ylim(-2.1,0)
plt.xlabel('Vmeas (µV)')
plt.ylabel('Iset (µA)')
for i in range(1,5):
    plt.axvline(i*-16.7,c='k',ls='--')
```

```python
fres = 2*echarge*16.7e-6/Planck/1e9
print(f'Resonance frequency at {fres:.6f} GHz')
```

# IV_vs_Bfield

```python
fname = 'M14_2017_03_13_10.41.22_IV_vs_magnet_isource'
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

```python
datashape = data[0].shape
datashape
```

```python
#data[len(data) // 4 * 3].head()
```

```python
mymtx = stlabutils.framearr_to_mtx(
    data, 'Vmeas (V)', xkey='Iset (V)', ykey='B (T)')
mymtx.applystep('rotate_ccw')
mymtx.applystep('flip 1,0')
#mymtx.applystep(f'crop 101,303,0,0')
#mymtx.outlier(100,0)
mymtx.applystep(f'sub_linecut 150,1')
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
plt.imshow(mymtx.pmtx/1e-3, aspect='auto', cmap='vlag', extent=np.array(
    extents)/1e-6, vmin=vmin, vmax=vmax)
#plt.ylim(-6,8)
cbar = plt.colorbar()
cbar.set_label('Vmeas (mV)')
plt.ylabel('Iset (uA)')
plt.xlabel('Bfield (µT)')
plt.title('Full overview')
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
dmtx.applystep('lowpass 0,1')
dmtx.applystep('yderiv 1')
```

```python hide_input=false
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = dmtx.getextents()

fig = plt.figure(figsize=cm2inch(17,8))
plt.imshow(dmtx.pmtx, aspect='auto', extent=np.array(
    extents)/1e-6, vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel('Bfield (µT)')
#plt.title('Full overview')
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
forbidden = []#173,905,998,1005,1172]
```

```python hide_input=false
bset, rn, iswitch, itrap = [], [], [], []
di = []

for i,vals in enumerate(mymtx.pmtx.T.values):

    bb = mymtx.pmtx.columns[i]
    Iset = mymtx.pmtx.index
    Vmeas = vals
    #Vmeas = Vmeas - Vmeas[len(Vmeas)//2] # remove voltage offset
    Rmeas = Vmeas/Iset
    dVdI = np.gradient(Vmeas,Iset)
    tridx = np.argmax(dVdI[len(dVdI)//2:]) + len(dVdI)//2
    swidx = np.argmax(dVdI[:len(dVdI)//2])

    Isw = Iset[swidx]
    Itr = abs(Iset[tridx])
    dI = np.diff(Iset)[0] # uncertainty in current: step size

    if i not in forbidden:
        bset.append(bb)
        rn.append(Rmeas[0])
        iswitch.append(Isw)
        itrap.append(Itr)
        di.append(dI)
    else:
        bset.append(np.nan)
        rn.append(np.nan)
        iswitch.append(np.nan)
        itrap.append(np.nan)
        di.append(np.nan)
        
bset = np.array(bset)
rn = abs(np.array(rn))
iswitch = np.array(iswitch)
itrap = np.array(itrap)
di = np.array(di)

fig,(ax1,ax2,ax3) = plt.subplots(1,3,figsize=(15,4))
plt.sca(ax1)
plt.plot(bset/1e-6,rn)
# plt.ylim(7.2,8.2)
plt.xlabel('Bfield (µT)')
plt.ylabel('Rn (Ohm)')
plt.sca(ax2)
plt.errorbar(bset/1e-6,iswitch/1e-6,di/1e-6,label='Iswitch',fmt='.-')
plt.errorbar(bset/1e-6,-itrap/1e-6,di/1e-6,label='Itrap',fmt='.-')
plt.ylabel('Critical currents (µA)')
plt.xlabel('Bfield (µT)')
plt.legend()
plt.sca(ax3)
#[plt.axvline(data[ff]['Vgate (V)'].values[0],c='C1') for ff in forbidden]
plt.errorbar(bset/1e-6,iswitch*rn/1e-6,di*rn/1e-6,fmt='.-')
plt.ylabel('IcRn product (µV)')
plt.xlabel('Bfield (µT)')
plt.tight_layout()
#filename = 'plots/'+devpath+'processing_fit_DC_fitdata.png'
#os.makedirs(os.path.dirname(filename), exist_ok=True)
#plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
pkldump = {
    'Vgate (V)':vgate,
    'Rn (Ohm)':rn,
    'Iswitch (A)':iswitch,
    'Itrap (A)':itrap,
    'dI (A)':di
}
```

```python
#pickle.dump(pkldump,open('pkldump/Hero_processing_fit_DC.pkl','wb'))
```

```python
plt.plot(bset/1e-6,iswitch)
plt.xlim(-150,-100)
```

```python
len(iswitch),len(1/bset)
```

```python
dBinuT = abs(np.diff(bset/1e-6)[0])
```

```python
y,x = plt.psd(iswitch, 512, 1 / dBinuT)
plt.xlabel('B (µT)')
```

```python
import peakutils
```

```python
x2,y2 = x,np.log10(y/min(y))
plt.plot(x2,y2)
indexes = peakutils.indexes(y2,thres=.1*max(y2))
plt.plot(x2[indexes],y2[indexes],'o')
print(x2[indexes])
```

```python
bperiod = 1/x2[indexes]
bperiod
```

```python
bnew = abs(bset/1e-6)/bperiod
bnew = bnew-22.5
```

```python
plt.plot(bnew,iswitch)
```

```python
# plot dVdI

wbval = (0.1, 0.1)
lims = np.percentile(dmtx.pmtx.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = dmtx.getextents()

fig = plt.figure(figsize=cm2inch(17,8))
plt.imshow(dmtx.pmtx, aspect='auto', extent=(bnew[0],bnew[-1],extents[2]/1e-6,extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel(r'Flux ($\Phi_0$)')
#plt.title('Full overview')
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
vmax = 100#lims[1]
extents = dmtx.getextents()

fig = plt.figure(figsize=cm2inch(17,8))
plt.imshow(dmtx.pmtx, aspect='auto', extent=np.array(extents)/1e-6, vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar()
cbar.set_label('dVdI (Ohm)')
plt.ylabel('Iset (µA)')
plt.xlabel(r'Flux ($\Phi_0$)')
plt.xlim(-120,-150)
plt.tight_layout()
#if plotall:
#    filename = 'plots/'+devpath+'processing_fit_DC_dVdI.png'
#    os.makedirs(os.path.dirname(filename), exist_ok=True)
#    plt.savefig(filename,bbox_to_inches='tight')
plt.show()
plt.close()
```

```python
lc = dmtx.pmtx.columns[270]
plt.plot(dmtx.pmtx.index/1e-6,dmtx.pmtx[lc])
plt.title(lc/1e-6)
plt.ylim(0,200)
plt.xlim(-3,0)
```

```python
plt.plot(mymtx.pmtx.index/1e-6,mymtx.pmtx[lc])
```

```python
xnew, ynew = [],[]
for i in range(270-12*10,270+12*10,12):
    lc = mymtx.pmtx.columns[i]
    xx = mymtx.pmtx[lc]/1e-6
    yy = mymtx.pmtx.index/1e-6
    xnew.append(xx)
    ynew.append(yy)
    plt.plot(xx,yy,'-',c='grey',alpha=0.5)
plt.xlim(-75,1)
plt.ylim(-3,0)
plt.xlabel('Vmeas (µV)')
plt.ylabel('Iset (µA)')
for i in range(1,5):
    plt.axvline(i*-16.7,c='k',ls='--')
```

```python
fres = 2*echarge*16.7e-6/Planck/1e9
print(f'Resonance frequency at {fres:.6f} GHz')
```

```python

```

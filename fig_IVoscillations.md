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
%run src/basemodules.py
```

```python
V1 = pickle.load(open('pkldump/processing_DC_2x1_Vg_15mK.pkl','rb'))
V2 = pickle.load(open('pkldump/processing_DC_2x1_Vg_1K.pkl','rb'))
B1 = pickle.load(open('pkldump/processing_DC_2x1_Bfield_15mK.pkl','rb'))
B2 = pickle.load(open('pkldump/processing_DC_Bfield.pkl','rb'))

V3 = pickle.load(open('pkldump/processing_DC_2x2_Vg.pkl','rb'))
```

```python
fig = plt.figure(figsize=cm2inch(17, 10), constrained_layout=True)
gs = fig.add_gridspec(2, 2)

ax1 = fig.add_subplot(gs[0, 0])
wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(V1.values, (wbval[0], 100 - wbval[1]))
vmin = 0 # lims[0]
vmax = 150 # lims[1]
extents = [V1.axes[1][0],V1.axes[1][-1],V1.axes[0][-1],V1.axes[0][0]]
plt.imshow(V1, aspect='auto', cmap='binary', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-6,0)
plt.xlim(0,30)
cbar = plt.colorbar()
cbar.set_label('dV/dI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('Gate voltage (V)')

ax2 = fig.add_subplot(gs[0, 1])
wbval = (0.1, 0.1)
cmap = 'PuBu_r'
lims = np.percentile(V3.values, (wbval[0], 100 - wbval[1]))
vmin = 0 # lims[0]
vmax = 200 # lims[1]
extents = [V3.axes[1][0],V3.axes[1][-1],V3.axes[0][-1],V3.axes[0][0]]
plt.imshow(V3, aspect='auto', cmap='binary', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax)
plt.ylim(-4,0)
cbar = plt.colorbar()
cbar.set_label('dV/dI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('Gate voltage (V)')

ax3 = fig.add_subplot(gs[1, 0])
wbval = (0.1, 0.1)
lims = np.percentile(B1.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = [B1.axes[1][0],B1.axes[1][-1],B1.axes[0][-1],B1.axes[0][0]]
plt.imshow(B1, aspect='auto', extent=np.array(
    extents)/1e-6, vmin=vmin, vmax=vmax,cmap='binary')
plt.ylim(-6,7.5)
cbar = plt.colorbar(location='right')
cbar.set_label('dV/dI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('Bfield (µT)')

ax4 = fig.add_subplot(gs[1, 1])
wbval = (0.1, 0.1)
lims = np.percentile(B2.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = [B2.axes[1][0],B2.axes[1][-1],B2.axes[0][-1],B2.axes[0][0]]
plt.imshow(B2, aspect='auto', extent=np.array(
    extents)/1e-6, vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='right')
cbar.set_label('dV/dI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('Bfield (µT)')

ax1.text(-.24, .95, "(a)", weight="bold", transform=ax1.transAxes)
ax2.text(-.25, .95, "(b)", weight="bold", transform=ax2.transAxes)
ax3.text(-.25, .95, "(c)", weight="bold", transform=ax3.transAxes)
ax4.text(-.25, .95, "(d)", weight="bold", transform=ax4.transAxes)

plt.savefig('figures/fig_IVosc.png', bbox_to_inches='tight', dpi=dpi)
plt.savefig('figures/fig_IVosc.pdf', bbox_to_inches='tight', dpi=dpi)
plt.show()
plt.close()
```

```python

```

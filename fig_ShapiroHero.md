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
f3 = pickle.load(open('pkldump/processing_Shapiro_Hero_3GHz.pkl','rb'))
f5 = pickle.load(open('pkldump/processing_Shapiro_Hero_5GHz.pkl','rb'))
f8 = pickle.load(open('pkldump/processing_Shapiro_Hero_8GHz.pkl','rb'))
```

```python
fig = plt.figure(figsize=cm2inch(17, 6), constrained_layout=True)
gs = fig.add_gridspec(1, 3)

ax1 = fig.add_subplot(gs[0, 0])
wbval = (0.1, 0.1)
lims = np.percentile(f3.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = [f3.axes[1][0],f3.axes[1][-1],f3.axes[0][-1],f3.axes[0][0]]
plt.imshow(f3, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='top')
cbar.set_label('dVdI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')

ax2 = fig.add_subplot(gs[0, 1])
wbval = (0.1, 0.1)
lims = np.percentile(f5.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = [f5.axes[1][0],f5.axes[1][-1],f5.axes[0][-1],f5.axes[0][0]]
plt.imshow(f5, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='top')
cbar.set_label('dVdI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')

ax3 = fig.add_subplot(gs[0, 2])
wbval = (0.1, 0.1)
lims = np.percentile(f8.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 100#lims[1]
extents = [f8.axes[1][0],f8.axes[1][-1],f8.axes[0][-1],f8.axes[0][0]]
plt.imshow(f8, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='top')
cbar.set_label('dVdI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')

ax1.text(-.24, .95, "(a)", weight="bold", transform=ax1.transAxes)
ax2.text(-.25, .95, "(b)", weight="bold", transform=ax2.transAxes)
ax3.text(-.25, .95, "(c)", weight="bold", transform=ax3.transAxes)

plt.savefig('figures/fig_ShapiroHero.png', bbox_to_inches='tight', dpi=dpi)
plt.savefig('figures/fig_ShapiroHero.pdf', bbox_to_inches='tight', dpi=dpi)
plt.show()
plt.close()
```

```python

```

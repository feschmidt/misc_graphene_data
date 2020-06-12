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
offres = pickle.load(open('pkldump/processing_Shapiro_2x1_5GHz.pkl','rb'))
onres = pickle.load(open('pkldump/processing_Shapiro_2x1_onres.pkl','rb'))
```

```python
fig = plt.figure(figsize=cm2inch(17, 6), constrained_layout=True)
gs = fig.add_gridspec(1, 2)

ax1 = fig.add_subplot(gs[0, 0])
wbval = (0.1, 0.1)
lims = np.percentile(offres.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 80#lims[1]
extents = [offres.axes[1][0],offres.axes[1][-1],offres.axes[0][-1],offres.axes[0][0]]
plt.imshow(offres, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='top')
cbar.set_label('dVdI (Ω)')
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.ylim(-10,10)

ax2 = fig.add_subplot(gs[0, 1])
wbval = (0.1, 0.1)
lims = np.percentile(onres.values, (wbval[0], 100 - wbval[1]))
vmin = 0#lims[0]
vmax = 80#lims[1]
extents = [onres.axes[1][0],onres.axes[1][-1],onres.axes[0][-1],onres.axes[0][0]]
plt.imshow(onres, aspect='auto', extent=(
    extents[0], extents[1], extents[2]/1e-6, extents[3]/1e-6), vmin=vmin, vmax=vmax,cmap='binary')
cbar = plt.colorbar(location='top')
cbar.set_label('dV/dI (Ω)')
#plt.gca().set_yticklabels([])
plt.ylabel('Iset (µA)')
plt.xlabel('RF power (dBm)')
plt.ylim(-10,10)

ax1.text(-.24, .95, "(a)", weight="bold", transform=ax1.transAxes)
ax2.text(-.25, .95, "(b)", weight="bold", transform=ax2.transAxes)

plt.savefig('figures/fig_Shapiro2x1.png', bbox_to_inches='tight', dpi=dpi)
plt.savefig('figures/fig_Shapiro2x1.pdf', bbox_to_inches='tight', dpi=dpi)
plt.show()
plt.close()
```

```python

```

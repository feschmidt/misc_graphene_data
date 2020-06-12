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
Vg = pickle.load(open('pkldump/IVsteps_Vg.pkl','rb'))
Bfield = pickle.load(open('pkldump/IVsteps_Bfield.pkl','rb'))
```

```python
fig = plt.figure(figsize=cm2inch(17, 6), constrained_layout=True)
gs = fig.add_gridspec(1, 2)

ax1 = fig.add_subplot(gs[0, 0])
plt.title('Varying $V_g$')
for xx,yy in zip(Vg[0],Vg[1]):
    plt.plot(xx,yy,'-',c='grey',alpha=0.3)
plt.xlim(-75,1)
plt.ylim(-2,0)
plt.xlabel('Vmeas (µV)')
plt.ylabel('Iset (µA)')
for i in range(1,5):
    plt.axvline(i*-16.7,c='k',ls='--',zorder=-1)

ax2 = fig.add_subplot(gs[0, 1])
plt.title('Varying Bfield')
for xx,yy in zip(Bfield[0],Bfield[1]):
    plt.plot(xx,yy,'-',c='grey',alpha=0.3)
plt.xlim(-75,1)
plt.ylim(-3,0)
plt.xlabel('Vmeas (µV)')
plt.ylabel('Iset (µA)')
for i in range(1,5):
    plt.axvline(i*-16.7,c='k',ls='--',zorder=-1)

ax1.text(-.24, .95, "(a)", weight="bold", transform=ax1.transAxes)
ax2.text(-.25, .95, "(b)", weight="bold", transform=ax2.transAxes)

plt.savefig('figures/fig_IVsteps.png', bbox_to_inches='tight', dpi=dpi)
plt.savefig('figures/fig_IVsteps.pdf', bbox_to_inches='tight', dpi=dpi)
plt.show()
plt.close()
```

```python

```

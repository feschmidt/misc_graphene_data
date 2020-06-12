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
xnew,ynew,fnew = pickle.load(open('pkldump/processing_Shapiro_Hero_lc.pkl','rb'))
freqdict = pickle.load(open('pkldump/processing_Shapiro_Hero_freq.pkl','rb'))
```

```python
fig = plt.figure(figsize=cm2inch(17, 6), constrained_layout=True)
gs = fig.add_gridspec(1, 2)

ax1 = fig.add_subplot(gs[0, 0])
for x,y,lc in zip(xnew,ynew,fnew):
    plt.plot(x,y,label=f'{lc/1e9:.0f} GHz')
plt.xlabel('Bias current (µA)')
plt.ylabel('Vmeas (µV)')
plt.legend(bbox_to_anchor=(1.04,1), loc="upper left")

ax2 = fig.add_subplot(gs[0, 1])
plt.plot(freqdict['RFfreq'],freqdict['Vpeak'],'o',c='grey',label='data',alpha=0.1,markeredgecolor=None)
for i in range(-8,9):
    if i==1:
        plt.plot(freqdict['xth'],freqdict['yth'],'k:',label='2ef/h',alpha=0.7)
    else:
        plt.plot(freqdict['xth'],i*freqdict['yth'],'k:',alpha=0.7)
    
plt.ylim(-70,70)
plt.xlabel('RF frequency (GHz)')
plt.ylabel('Voltage step (µV)')
plt.legend(loc=4)


ax1.text(-.24, .95, "(a)", weight="bold", transform=ax1.transAxes)
ax2.text(-.25, .95, "(b)", weight="bold", transform=ax2.transAxes)

plt.savefig('figures/fig_ShapiroHeroFreq.png', bbox_to_inches='tight', dpi=dpi)
plt.savefig('figures/fig_ShapiroHeroFreq.pdf', bbox_to_inches='tight', dpi=dpi)
plt.show()
plt.close()
```

```python

```

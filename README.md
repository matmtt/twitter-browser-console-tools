## Mass Repost Removal Tool

F12 on browser
Go to console tab

Run the following on your X profile to remove reposts:

```javascript
// Script para remover retuítes em massa — adaptado ao seletor data-testid="unretweetConfirm"
// Cole no Console (F12) e pressione Enter

(async () => {
  const delay = ms => new Promise(r => setTimeout(r, ms));
  const clickDelay = 800;
  const popupDelay = 600;
  const limit = 300; // máximo de retuítes a remover

  function findRetweetButtons() {
    return Array.from(document.querySelectorAll('button[data-testid="unretweet"]'))
      .filter(b => b.offsetParent !== null);
  }

  async function confirmUnretweet() {
    for (let i = 0; i < 15; i++) { // tenta por ~1s achar o botão de confirmação
      const confirmBtn = document.querySelector('[data-testid="unretweetConfirm"]');
      if (confirmBtn && confirmBtn.offsetParent !== null) {
        confirmBtn.click();
        return true;
      }
      await delay(100);
    }
    return false;
  }

  let count = 0;
  while (count < limit) {
    const btns = findRetweetButtons();
    if (btns.length === 0) break;

    const btn = btns[0];
    btn.scrollIntoView({block: 'center'});
    btn.click();
    await delay(popupDelay);
    await confirmUnretweet();
    count++;
    await delay(clickDelay);
  }

  console.log(`Retuítes removidos: ${count}`);
})();
```

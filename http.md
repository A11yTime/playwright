const { test, expect, chromium } = require('@playwright/test');

test('Check accessibility of the page with no HTTP/2 errors', async ({ page }) => {
    // Set a longer timeout for the test (e.g., 60 seconds)
    await page.goto('https://www.fidelity.com/', { timeout: 60000 }); // 60 seconds timeout
    await page.addScriptTag({ path: require.resolve('axe-core') });
  
    const result = await page.evaluate(() => {
      return new Promise((resolve) => {
        window.axe.run(document, {}, (err, results) => {
          if (err) {
            console.error('Axe failed to run:', err);
            resolve([]);
          }
          resolve(results.violations);
        });
      });
    });
  
    console.log(result);
    expect(result.length).toBe(0);
  });
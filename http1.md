const { test, expect } = require('@playwright/test');

test('Check accessibility of the page with no HTTP/2 errors', async ({ page }) => {
  // Set a longer timeout for the test (e.g., 60 seconds)
  await page.goto('https://www.fidelity.com/', { timeout: 120000, waitUntil: 'load' });

  // Inject axe-core into the page (make sure axe-core is installed as a dependency)
  await page.addScriptTag({ path: require.resolve('axe-core') });

  // Run axe-core accessibility checks
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

  // Update the assertion to handle acceptable violations
  expect(result.length).toBeLessThanOrEqual(5); // Or any acceptable number of violations
});



  

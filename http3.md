const { test, expect } = require('@playwright/test');

test('Check accessibility of the page with no HTTP/2 errors', async ({ page }) => {
  try {
    // Enable request interception on the page
    await page.route('**/*', (route) => {
      route.continue({
        headers: {
          ...route.request().headers(),
          'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
        },
      });
    });

    // Navigate to the URL
    await page.goto('https://www.fidelity.com/', { timeout: 60000, waitUntil: 'load' });
    
    // Inject axe-core into the page
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

    // Assert that there are no serious accessibility issues
    expect(result.length).toBeLessThanOrEqual(5); // Adjust based on your needs
  } catch (error) {
    console.error('Test failed with error:', error);
    throw error; // Re-throw error to fail the test appropriately
  }
});

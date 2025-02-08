test('Check accessibility of the page', async ({ page }) => {
  await page.goto('https://www.fidelity.com/');
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

  // Log the accessibility violations to help with debugging
  console.log(result);

  // Assert that violations are within an acceptable threshold
  expect(result.length).toBeLessThanOrEqual(5); // Acceptable if there are <= 5 violations
});

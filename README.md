# terriblyassignment
Detaial explanation of code i have commented that part.
import React, { useState } from 'react';
import Chart from 'chart.js';

const WordFrequencyApp = () => {
  const [wordFrequency, setWordFrequency] = useState({});
  const [csvData, setCsvData] = useState('');

  const fetchWordFrequency = async () => {
    try {
      const response = await fetch('https://www.terriblytinytales.com/test.txt');
      const content = await response.text();

      // Parse the content and calculate word frequency
      const words = content.split(/\s+/);
      const frequency = {};
      words.forEach((word) => {
        frequency[word] = frequency[word] ? frequency[word] + 1 : 1;
      });

      // Sort the word frequency in descending order
      const sortedWords = Object.keys(frequency).sort(
        (a, b) => frequency[b] - frequency[a]
      );

      // Take the top 20 most occurring words
      const topWords = sortedWords.slice(0, 20);

      // Create data for the histogram
      const histogramData = topWords.map((word) => frequency[word]);

      // Update state with word frequency data
      setWordFrequency(frequency);

      // Generate CSV data
      const csvContent = `Word,Frequency\n${topWords
        .map((word) => `${word},${frequency[word]}`)
        .join('\n')}`;
      setCsvData(csvContent);

      // Plot the histogram
      const ctx = document.getElementById('chart').getContext('2d');
      new Chart(ctx, {
        type: 'bar',
        data: {
          labels: topWords,
          datasets: [
            {
              label: 'Word Frequency',
              data: histogramData,
              backgroundColor: 'rgba(75, 192, 192, 0.6)',
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            y: {
              beginAtZero: true,
              stepSize: 1,
            },
          },
        },
      });
    } catch (error) {
      console.log('Error fetching word frequency:', error);
    }
  };

  const handleExport = () => {
    const element = document.createElement('a');
    const file = new Blob([csvData], { type: 'text/csv' });
    element.href = URL.createObjectURL(file);
    element.download = 'word_frequency.csv';
    document.body.appendChild(element);
    element.click();
    document.body.removeChild(element);
  };

  return (
    <div>
      <button onClick={fetchWordFrequency}>Submit</button>
      <div>
        <canvas id="chart" width="400" height="300"></canvas>
      </div>
      <button onClick={handleExport}>Export</button>
    </div>
  );
};

export default WordFrequencyApp;

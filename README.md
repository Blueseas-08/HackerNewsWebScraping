# Hacker News Scraper

This script scrapes the top stories from the first two pages of Hacker News and filters them based on their votes. Only stories with more than 99 votes are included in the output, which is sorted by the number of votes in descending order.

## Dependencies

- `requests`
- `beautifulsoup4`
- `pprint`

These can be installed via pip if they are not already available in your environment:

```sh
pip install requests beautifulsoup4
```

## Usage

1. **Import the necessary libraries:**

    ```python
    import requests
    from bs4 import BeautifulSoup
    import pprint
    ```

2. **Make HTTP GET requests to the Hacker News pages:**

    ```python
    res = requests.get('https://news.ycombinator.com/news')
    res2 = requests.get('https://news.ycombinator.com/news?p=2')
    ```

3. **Parse the HTML content using BeautifulSoup:**

    ```python
    soup = BeautifulSoup(res.text, 'html.parser')
    soup2 = BeautifulSoup(res2.text, 'html.parser')
    ```

4. **Select the story links and subtext for votes:**

    ```python
    links = soup.select('.storylink')
    subtext = soup.select('.subtext')
    links2 = soup2.select('.storylink')
    subtext2 = soup2.select('.subtext')
    ```

5. **Combine the links and subtext from both pages:**

    ```python
    mega_links = links + links2
    mega_subtext = subtext + subtext2
    ```

6. **Define a function to sort stories by votes:**

    ```python
    def sort_stories_by_votes(hnlist):
        return sorted(hnlist, key=lambda k: k['votes'], reverse=True)
    ```

7. **Define a function to create a custom Hacker News list based on the votes:**

    ```python
    def create_custom_hn(links, subtext):
        hn = []
        for idx, item in enumerate(links): ##enumerate gives us indexes
            title = item.getText()
            href = item.get('href', None)
            vote = subtext[idx].select('.score')
            if len(vote):
                points = int(vote[0].getText().replace(' points', ''))
                if points > 99:
                    hn.append({'title': title, 'link': href, 'votes': points})
        return sort_stories_by_votes(hn)
    ```

8. **Print the sorted list of stories:**

    ```python
    pprint.pprint(create_custom_hn(mega_links, mega_subtext))
    ```

## Output

The script outputs a list of dictionaries, each containing the title, link, and votes of stories with more than 99 votes, sorted in descending order of votes.

Example output:

```python
[{'title': 'Story 1', 'link': 'http://example.com/story1', 'votes': 150},
 {'title': 'Story 2', 'link': 'http://example.com/story2', 'votes': 120},
 {'title': 'Story 3', 'link': 'http://example.com/story3', 'votes': 110}]
```

## Notes

- Ensure that you have internet connectivity to fetch the pages from Hacker News.
- This script is designed to run in a Python environment with the required libraries installed.

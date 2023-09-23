import requests
import urllib.robotparser
from time import sleep
from urllib.parse import urlparse, urljoin
from bs4 import BeautifulSoup

# Function to check if a URL is allowed by robots.txt
def is_url_allowed(url, user_agent="Mr_Crawly"):
    rp = urllib.robotparser.RobotFileParser()
    rp.set_url(urljoin(url, "/robots.txt"))
    rp.read()
    return rp.can_fetch(user_agent, url)

# Function to fetch and parse a URL
def fetch_and_parse_url(url, output_file):
    try:
        response = requests.get(url)
        if response.status_code == 200:
            soup = BeautifulSoup(response.text, "html.parser")
            # Extract and print the title of the page
            page_title = soup.title.string.strip() if soup.title else "No title found"
            print(f"Crawled: {url}")
            print(f"Title: {page_title}\n")
            # Save crawled data to the output file
            with open(output_file, "a", encoding="utf-8") as file:
                file.write(f"Crawled: {url}\n")
                file.write(f"Title: {page_title}\n\n")
    except Exception as e:
        print(f"Failed to crawl {url}: {str(e)}")

# Function to crawl a URL with a crawl delay
def crawl_url_with_delay(url, output_file, depth=0, max_depth=3, delay=2):
    if depth > max_depth:
        return
    if is_url_allowed(url):
        fetch_and_parse_url(url, output_file)
        try:
            sleep(delay)  # Add a crawl delay
            response = requests.get(url)
            if response.status_code == 200:
                soup = BeautifulSoup(response.text, "html.parser")
                # Extract links from the page and recursively crawl them
                for link in soup.find_all("a"):
                    href = link.get("href")
                    if href and not href.startswith("#"):  # Ignore anchor links
                        next_url = urljoin(url, href)
                        crawl_url_with_delay(next_url, output_file, depth + 1, max_depth, delay)
        except Exception as e:
            print(f"Failed to crawl {url}: {str(e)}")
    else:
        print(f"URL {url} is not allowed by robots.txt")

# Main function to automate crawling behavior
def main():
    num_sites_to_crawl = 10
    sites_crawled = 0
    output_file = "crawled_data.txt"  # Name of the output file

    while sites_crawled < num_sites_to_crawl:
        starting_url = input("Enter the starting URL: ")
        crawl_url_with_delay(starting_url, output_file, delay=2)
        sites_crawled += 1

        if sites_crawled < num_sites_to_crawl:
            choice = input("Do you want to continue? (yes/no): ").strip().lower()
            if choice != "yes":
                break

if __name__ == "__main__":
    main()

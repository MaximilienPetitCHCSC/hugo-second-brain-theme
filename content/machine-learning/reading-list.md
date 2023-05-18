---
title: Reading List
linktitle: Reading List
date: '2019-05-05T00:00:00+01:00'
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---

<!DOCTYPE html>
<html>
<body>
    <h1 style="text-align:center; font-weight:bold;">La veille du CHCSC</h1>
    <div id="rssContent"></div>
    <div id="rssContent2"></div>
    <div id="mergedRssContent"></div>

    <style>
        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #4A90E2;
            color: white;
        }
        tr:hover {
            background-color: #F5F5F5;
        }
    </style>

    <script>
        const RSS_URL1 = 'https://api.archives-ouvertes.fr/search/halshs/?q=title_t:(patrimoine%20OR%20ecologie%20OR%20environnement)&wt=rss&fq=submittedDateY_i:[2022%20TO%20*]&fq=submitType_s:file&rows=100';
        const RSS_URL2 = 'https://api.archives-ouvertes.fr/search/halshs/?q=title_t:(cin%C3%A9ma%20OR%20%22histoire%20culturelle%22)&wt=rss&fq=submittedDateY_i:[2022%20TO%20*]&fq=submitType_s:file&rows=100';

        function fetchAndDisplayRss(RSS_URL, contentDivId) {
            return fetch(RSS_URL)
                .then(response => response.text())
                .then(str => new window.DOMParser().parseFromString(str, "text/xml"))
                .then(data => {
                    const items = data.querySelectorAll("item");
                    let html = '';
                    items.forEach(el => {
                        let description = el.querySelector("description").textContent;
                        description = description.length > 100 ? description.substring(0, 100) + '...' : description;
                        html += `<tr><td>${el.querySelector("title").textContent}</td>`;
                        html += `<td><a href="${el.querySelector("link").textContent}">${el.querySelector("link").textContent}</a></td>`;
                        html += `<td>${el.querySelector("pubDate").textContent}</td>`;
                        html += `<td>${el.querySelector("dc\\:creator, creator").textContent}</td>`;
                        html += `<td>${description}</td></tr>`;
                    });
                    return html;
                });
        }

        function mergeRssResults() {
            const rssContent1Promise = fetchAndDisplayRss(RSS_URL1, "#rssContent");
            const rssContent2Promise = fetchAndDisplayRss(RSS_URL2, "#rssContent2");

            Promise.all([rssContent1Promise, rssContent2Promise]).then(results => {
                const mergedHtml = `<table><tr><th>Titre</th><th>Lien</th><th>Date de publication</th><th>Auteur</th><th>Résumé</th></tr>${results[0]}${results[1]}</table>`;
                document.querySelector("#mergedRssContent").innerHTML = mergedHtml;

                // Ajout de la gestion des événements pour la surbrillance des lignes
                const rows = document.querySelectorAll("#mergedRssContent tr");
                rows.forEach(row => {
                    row.addEventListener("mouseover", () => {
                        row.style.backgroundColor = "#F5F5F5";
                    });
                    row.addEventListener("mouseout", () => {
                        row.style.backgroundColor = "";
                    });
                });
            });
        }

        mergeRssResults();
    </script>
</body>
</html>

## Command to create the output.json files for characters
# List of IDs
$ids = 1..4

foreach ($id in $ids) {
    # Read the base query from a template
    $query = @"
query {
  character(id: $id) {
    id
    name
    status
    species
    type
    gender
  }
}
"@

    # Flatten query to one line and escape quotes
    $query = $query -replace "`r`n", " " -replace "`n", " " -replace '"', '\"'

    # Build JSON body
    $json = "{""query"": ""$query""}"

    # Send POST request
    $response = Invoke-RestMethod `
        -Uri "https://rickandmortyapi.com/graphql" `
        -Method POST `
        -ContentType "application/json" `
        -Body $json

    # Save output to separate file
    $outfile = "character-id-$id-output.json"
    $response | ConvertTo-Json -Depth 10 | Out-File $outfile -Encoding utf8

    Write-Host "✅ Created $outfile"
}



## Command to create the output.json files for pages
# Pages to fetch
$pages = 1..4

foreach ($page in $pages) {
    # Build the GraphQL query for the page
    $query = @"
query {
  characters(page: $page) {
    results {
      id
      name
      status
      image
    }
  }
}
"@

    # Flatten the query to one line and escape quotes
    $query = $query -replace "`r`n", " " -replace "`n", " " -replace '"', '\"'

    # Build JSON body
    $json = "{""query"": ""$query""}"

    # Send the POST request
    $response = Invoke-RestMethod `
        -Uri "https://rickandmortyapi.com/graphql" `
        -Method POST `
        -ContentType "application/json" `
        -Body $json

    # Save output to a separate JSON file for each page
    $outfile = "characters-page-$page-output.json"
    $response | ConvertTo-Json -Depth 10 | Out-File $outfile -Encoding utf8

    Write-Host "✅ Created $outfile"
}


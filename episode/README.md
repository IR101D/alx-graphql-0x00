# List of episode IDs you want to fetch
$episodeIDs = 1..4

foreach ($id in $episodeIDs) {
    # Build GraphQL query for the episode
    $query = @"
query {
  episode(id: $id) {
    id
    name
    air_date
    episode
  }
}
"@

    # Flatten query and escape quotes
    $query = $query -replace "`r`n", " " -replace "`n", " " -replace '"', '\"'

    # Build JSON body
    $json = "{""query"": ""$query""}"

    # Send POST request
    $response = Invoke-RestMethod `
        -Uri "https://rickandmortyapi.com/graphql" `
        -Method POST `
        -ContentType "application/json" `
        -Body $json

    # Save output to a separate JSON file
    $outfile = "characters-page$id-output.json"
    $response | ConvertTo-Json -Depth 10 | Out-File $outfile -Encoding utf8

    Write-Host "✅ Created $outfile"
}

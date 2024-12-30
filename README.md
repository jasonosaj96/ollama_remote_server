# Ollama Remote Setup Guide

This repository provides instructions for setting up Ollama on a remote server with secure tunneling for both text-to-text and text-to-image generation capabilities.

## Prerequisites

- SSH access to the host server
- Sudo privileges on the host server
- Basic understanding of terminal commands

## Installation Steps

1. Install Ollama on the host server by following the official installation guide at [Ollama.ai](https://ollama.ai)

2. Configure Ollama to accept remote connections:
   ```bash
   sudo systemctl edit ollama.service
   ```

3. In the editor, add the following configuration under the section "Anything between here and the comment below will become the new contents of the file":
   ```ini
   [Service]
   Environment="OLLAMA_HOST=0.0.0.0"
   ```

4. Apply the changes by restarting the Ollama service:
   ```bash
   sudo systemctl daemon-reload && sudo systemctl restart ollama
   ```

5. Create an SSH tunnel to securely connect to the Ollama service:
   ```bash
   ssh -L 8080:localhost:11434 jason-server@some_host_Address -p 8000
   ```

## Testing the Setup

### Text-to-Text Generation

1. Test basic text generation using curl:
   ```bash
   curl -X POST http://localhost:8080/api/generate -d '{
     "model": "llama2",
     "prompt": "Hello, how are you?"
   }'
   ```

2. Or use the Python API:
   ```python
   import requests

   response = requests.post('http://localhost:8080/api/generate', 
       json={
           'model': 'llama2',
           'prompt': 'Hello, how are you?'
       }
   )
   print(response.json()['response'])
   ```

### Text-to-Image Generation

1. Test image generation using curl:
   ```bash
   curl -X POST http://localhost:8080/api/generate -d '{
     "model": "stable-diffusion",
     "prompt": "A beautiful sunset over mountains"
   }' > image.png
   ```

2. Or use the Python API:
   ```python
   import requests

   response = requests.post('http://localhost:8080/api/generate',
       json={
           'model': 'stable-diffusion',
           'prompt': 'A beautiful sunset over mountains'
       }
   )
   with open('generated_image.png', 'wb') as f:
       f.write(response.content)
   ```

## Troubleshooting

1. If you can't connect to the service, verify:
   - Ollama service is running (`sudo systemctl status ollama`)
   - SSH tunnel is active
   - Firewall settings allow the connection
   - The correct port numbers are being used

2. For permission errors:
   - Check that Ollama has correct file permissions
   - Verify your user has necessary permissions

## Security Considerations

- The SSH tunnel provides secure access to the Ollama service
- Avoid exposing the Ollama port (11434) directly to the internet
- Regularly update Ollama and your system for security patches
- Use strong SSH keys and passwords

## Contributing

Feel free to open issues or submit pull requests if you have suggestions for improving this setup guide.

## License

This project is licensed under the MIT License - see the LICENSE file for details.